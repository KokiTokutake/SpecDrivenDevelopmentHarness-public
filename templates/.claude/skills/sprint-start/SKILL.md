# /sprint-start — 新スプリント開始ウィザード

**使う場面**: 工程4（タスク管理）。新しいスプリントを開始するとき。スプリントゴールの設定・タスクのアサイン・GitHub Project Iteration の作成を行う。

---

## 目的

GitHub Project の Iteration フィールドを使ってスプリントを管理する。
タスクを選択し、対応する GitHub Issues を Iteration（スプリント）に割り当てる。

---

## 実行手順

### STEP 0: GitHub Project 設定の確認

`.github/project-config.json` が存在し、`project_number` が設定されているか確認する:

```bash
test -f .github/project-config.json && \
  jq -e '.project_number != 0 and .project_number != null' .github/project-config.json
```

**存在しない・または未設定の場合、以下を表示して停止する:**

```
⛔ GitHub Project が設定されていません。

スプリント（Iteration）を作成するには GitHub Project が必要です。

先に実行してください:
  /github-project-init → 既存プロジェクトに GitHub Project を後付けセットアップ
```

### STEP 1: 前スプリントの確認

現在アクティブなスプリント（Iteration）がないか確認する:

```bash
OWNER=$(jq -r '.owner' .github/project-config.json)
PROJECT_NUMBER=$(jq -r '.project_number' .github/project-config.json)

# 現在の Iteration 状況を確認
gh project field-list $PROJECT_NUMBER --owner $OWNER --format json \
  | jq '.fields[] | select(.name == "Sprint") | .configuration.iterations'
```

アクティブなスプリントが存在する場合:
```
Sprint N が進行中です。先に /sprint-end を実行してください。
```

---

### STEP 2: スプリント情報の収集

以下を質問する:

```
質問1: スプリント名を教えてください。
       提案: 「Sprint <前回+1>」
       例: Sprint 2

質問2: このスプリントのゴールを教えてください。
       例: 「ダッシュボード機能の完成とパフォーマンス改善」

質問3: 開始日は？（デフォルト: 今日 YYYY-MM-DD）

質問4: 終了日は？（デフォルト: 開始日 + 2週間）
```

---

### STEP 3: GitHub Project Iteration の追加

新しい Iteration をスプリントとして追加する。

まず GraphQL ミューテーションで試みる:

```bash
PROJECT_ID=$(jq -r '.project_id' .github/project-config.json)
SPRINT_FIELD_ID=$(jq -r '.fields.sprint.id' .github/project-config.json)

gh api graphql -f query='
  mutation($projectId: ID!, $fieldId: ID!, $title: String!, $startDate: String!, $duration: Int!) {
    addProjectV2IterationFieldIteration(input: {
      projectId: $projectId
      fieldId: $fieldId
      startDate: $startDate
      title: $title
      duration: $duration
    }) {
      iteration { id title startDate duration }
    }
  }
' -f projectId="$PROJECT_ID" \
  -f fieldId="$SPRINT_FIELD_ID" \
  -f title="<スプリント名>" \
  -f startDate="<開始日 YYYY-MM-DD>" \
  -F duration=14
```

**GraphQL が成功した場合**: 返却された `iteration.id` を記録して STEP 4 へ進む。

**GraphQL が失敗した場合（`Field 'addProjectV2IterationFieldIteration' doesn't exist` 等）**:

> GitHub Projects V2 の Iteration 追加は API の提供状況によって CLI から実行できない場合がある。
> その場合は UI から手動で追加する:

```
📋 GitHub UI でのイテレーション追加手順:

1. https://github.com/orgs/<owner>/projects/<number>/settings へアクセス
2. 左サイドバーの「Sprint」フィールド（または作成した Iteration フィールド）をクリック
3. 「+ Add iteration」ボタンをクリック
4. 以下を入力:
   - タイトル: <スプリント名>
   - 開始日:   <開始日>
   - 期間:     <日数（例: 14日）>
5. 「Save」をクリック

追加できたら「完了しました」と教えてください。Iteration ID を取得して続行します。
```

UI で追加後、Iteration ID を取得する:

```bash
gh api graphql -f query='
  query($projectId: ID!) {
    node(id: $projectId) {
      ... on ProjectV2 {
        fields(first: 20) {
          nodes {
            ... on ProjectV2IterationField {
              name
              configuration { iterations { id title startDate duration } }
            }
          }
        }
      }
    }
  }
' -f projectId="$PROJECT_ID" \
  | jq '.data.node.fields.nodes[] | select(.name == "Sprint") | .configuration.iterations[-1]'
```

取得した Iteration ID を記録して STEP 4 へ進む。

---

### STEP 4: タスクの選択とスプリント割り当て

`tasks/task-list.md` からバックログ（未割り当て・未着手）のタスクを表示する:

```
📋 バックログ（スプリント未割り当て）:

  番号  | Task ID   | タイトル             | SP | Spec ID       | Issue
  ------|-----------|---------------------|----|---------------|------
  1     | TASK-005  | ダッシュボードAPI    | 3  | SPEC-DASH-001 | #48
  2     | TASK-006  | 通知コンポーネント   | 2  | SPEC-NOTIF-001| #49

このスプリントに含めるタスク番号をカンマ区切りで入力してください。
例: 1, 2
（Enter でスキップ → 全タスクをバックログのままにする）

選択したタスクの合計 SP: 5 SP
続けますか？ (Y/n)
```

---

### STEP 5: GitHub Issues を Iteration に割り当て

選択した各タスクの GitHub Issue を Project Iteration に割り当てる:

```bash
PROJECT_ID=$(jq -r '.project_id' .github/project-config.json)
PROJECT_NUMBER=$(jq -r '.project_number' .github/project-config.json)
OWNER=$(jq -r '.owner' .github/project-config.json)
SPRINT_FIELD_ID=$(jq -r '.fields.sprint.id' .github/project-config.json)

# 各 Issue の Project Item ID を取得し、Iteration を設定
for ISSUE_NUMBER in <選択した Issue 番号のリスト>; do
  ITEM_ID=$(gh project item-list $PROJECT_NUMBER --owner $OWNER --format json \
    | jq -r --argjson n $ISSUE_NUMBER '.items[] | select(.content.number == $n) | .id')

  gh api graphql -f query='
    mutation($projectId: ID!, $itemId: ID!, $fieldId: ID!, $iterationId: String!) {
      updateProjectV2ItemFieldValue(input: {
        projectId: $projectId
        itemId: $itemId
        fieldId: $fieldId
        value: { iterationId: $iterationId }
      }) {
        projectV2Item { id }
      }
    }
  ' -f projectId="$PROJECT_ID" \
    -f itemId="$ITEM_ID" \
    -f fieldId="$SPRINT_FIELD_ID" \
    -f iterationId="<作成した Iteration ID>"
done
```

---

### STEP 6: スプリントラベルの付与

選択した Issue にスプリントラベルを付与する（任意だが推奨）:

```bash
# スプリントラベルを作成（初回のみ）
gh label create "sprint:<スプリント名>" \
  --color "0075ca" \
  --description "<スプリント名> のタスク"

# 各 Issue にラベルを付与
gh issue edit <issue番号> --add-label "sprint:<スプリント名>"
```

---

### STEP 7: 完了サマリー

```
✅ スプリント開始完了！

Sprint:   <スプリント名>
ゴール:   <ゴール>
期間:     <開始日> 〜 <終了日>（N 日間）
タスク:   N 件 / 合計 X SP

GitHub Project:
  https://github.com/orgs/<owner>/projects/<number>

次のアクション:
1. GitHub Project ボードでタスクの割り当てを確認する
2. 各担当者が /task-start [TASK-ID] でタスクを開始する
3. /task-board でスプリントの進捗を確認する（朝会・進捗確認）
```

---

## 注意事項

- スプリントゴールは Constitution.md・承認済み Spec と整合していること
- 未承認の Spec に紐付くタスクを含める場合は警告を表示する:
  「TASK-XXX は未承認の SPEC-XXX-XXX (status: draft) に紐付いています。
   スプリント開始前に仕様書を承認することを推奨します。」
- `gh` コマンドが認証されていない場合は `gh auth login` を案内する

---

## 関連スキル

- `tasks-plan` — タスクの作成・GitHub Issues 生成
- `sprint-end` — スプリント終了処理
- `task-board` — タスク一覧の確認
- `task-start` — タスク実装の開始
