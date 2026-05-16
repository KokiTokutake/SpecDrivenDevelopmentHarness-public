# 仕様駆動開発ハーネス（SDD Harness）

書籍「仕様駆動開発 実践入門 〜 AIで実現する開発方法論」の手法を、  
**誰でも即座に使える**テンプレート・ワークフロー・自動化スクリプトとして実装したハーネスです。

---

## 仕様駆動開発（SDD）とは

**仕様をコードと同じように Markdown で記述し、Git でバージョン管理し、AI がレビュー・生成を支援する**開発手法。

### 4つの基本原則

| 原則 | 説明 |
|------|------|
| 生きたドキュメント | 仕様書は実装と並走して継続更新する |
| 唯一の情報源 | 仕様書が Single Source of Truth |
| 変更と反復が前提 | 仕様は変わる。変更プロセスを設計する |
| AIでコスト削減 | AI を活用し、仕様書作成・レビュー・実装を加速する |

### 7つの工程

```
Constitution → Specify → Plan → Tasks → Implement → Verify → Migration/Operation
    ↑                                                               |
    └─────────────────── フィードバック ───────────────────────────┘
```

| # | 工程 | 主な成果物 |
|---|------|-----------|
| 1 | 原則決定（Constitution） | `Constitution.md`（プロジェクト憲章） |
| 2 | 企画・要件定義（Specify） | 仕様書（Spec）、GitHub Issues |
| 3 | 設計計画（Plan） | アーキテクチャ図、設計ドキュメント |
| 4 | タスク分割（Tasks） | タスク一覧、見積もり |
| 5 | 実装（Implement） | コード、PR |
| 6 | 検証・受入（Verify / Accept） | テスト結果、受入確認 |
| 7 | 移行・運用（Migration / Operation） | 運用手順、次サイクルへのフィードバック |

---

## ハーネスの使い方

### 1. テンプレートをコピーする

```bash
# GitHub CLI でリポジトリ作成
gh repo create [org]/[repo-name] --private --clone
cd [repo-name]

# ハーネスのテンプレートをコピー
cp -r /path/to/sdd-harness/templates/. .
git add .
git commit -m "add: SDD ハーネステンプレートを追加"
git push origin main
```

### 2. Constitution.md を作成する

```bash
# テンプレートを編集してプロジェクト固有の内容を記入
code .constitution/Constitution.md
```

`Constitution.md` はプロジェクトの原則・制約・用語集を定義します。  
**全ての開発判断の基準**となるため、最初に作成してチームで合意します。

### 3. GitHub を設定する

```bash
# ガイドに従って Branch Protection / CODEOWNERS / ラベルを設定
cat docs/guides/github-setup.md
```

### 4. 仕様書を書いてから実装する

```bash
# 仕様書テンプレートを選んでコピー
cp docs/spec/feature-spec.md docs/spec/[機能名].md
# 仕様書を編集 → PR → レビュー → 承認 → 実装
```

---

## テンプレート一覧

### 工程1: 原則決定（Constitution）

| ファイル | 説明 |
|---------|------|
| `.constitution/Constitution.md` | プロジェクト憲章（原則・ステークホルダー・用語集） |

### 工程2: 企画・要件定義（Specify）

| ファイル | 説明 |
|---------|------|
| `docs/spec/feature-spec.md` | 機能仕様書（Why / What / 受入基準） |
| `docs/spec/api-spec.md` | REST API 仕様書（エンドポイント・リクエスト・レスポンス） |
| `docs/spec/ui-spec.md` | UI 仕様書（レイアウト・状態・アクセシビリティ） |
| `docs/spec/_README.md` | 仕様書の書き方・Spec ID 命名規則 |
| `.github/ISSUE_TEMPLATE/feature-request.yml` | 機能追加 Issue テンプレート |
| `.github/ISSUE_TEMPLATE/spec-change.yml` | 仕様変更 Issue テンプレート |
| `.github/ISSUE_TEMPLATE/bug-report.yml` | バグ報告 Issue テンプレート |
| `.github/pull_request_template.md` | 標準 PR テンプレート |
| `.github/PULL_REQUEST_TEMPLATE/spec-change.md` | 仕様書変更 PR テンプレート（5軸レビュー） |

### 工程3: 設計計画（Plan）

| ファイル | 説明 |
|---------|------|
| `docs/design/architecture.md` | システムアーキテクチャ設計書 |
| `docs/design/component-design.md` | コンポーネント詳細設計書 |
| `docs/design/_README.md` | 設計ドキュメントの使い方 |

### 工程4: タスク分割（Tasks）

| ファイル | 説明 |
|---------|------|
| `tasks/task-list.md` | タスク一覧（Spec ID 紐付き・ストーリーポイント） |
| `tasks/_README.md` | タスク粒度ルール（1タスク = 1PR = 80分以内） |

### 工程5: 実装（Implement）

| ファイル | 説明 |
|---------|------|
| `CLAUDE.md` | Claude Code 向け プロジェクト設定（仕様書・コーディングルール） |
| `docs/guides/ai-workflow.md` | Claude Code CLI / Codex 活用ガイド・プロンプトパターン集 |
| `.github/workflows/ci.yml` | CI パイプライン（仕様書チェック・テスト自動化） |

### 工程6: 検証・受入（Verify / Accept）

| ファイル | 説明 |
|---------|------|
| `docs/guides/test-plan.md` | テスト計画書（テストタイプ・テストケース表） |
| `docs/guides/acceptance-checklist.md` | 受入確認チェックリスト（PO サインオフ） |

### 工程7: 移行・運用（Migration / Operation）

| ファイル | 説明 |
|---------|------|
| `docs/guides/operation-runbook.md` | 運用ランブック（アラートレベル・障害対応フロー） |
| `docs/guides/release-notes.md` | リリースノートテンプレート（Spec ID 参照） |
| `docs/guides/retrospective.md` | レトロスペクティブ（KPT・メトリクス・次サイクルへのフィードバック） |

### 導入支援

| ファイル | 説明 |
|---------|------|
| `docs/guides/github-setup.md` | GitHub リポジトリ設定手順（規模別推奨設定） |
| `docs/guides/onboarding-roadmap.md` | 導入ロードマップ（PHASE 1〜5、当日〜12ヶ月） |
| `docs/guides/checklists.md` | 役割別・フェーズ別チェックリスト・トラブルシューティング |
| `.github/CODEOWNERS` | 領域別レビュアー定義テンプレート |

---

## ディレクトリ構造（コピー後）

```
your-project/
├── .constitution/
│   └── Constitution.md          # ← 最初に作成・チームで合意
├── docs/
│   ├── spec/                    # ← 仕様書（実装前に必ず作成）
│   │   ├── _README.md
│   │   ├── feature-spec.md      # 機能仕様書テンプレート
│   │   ├── api-spec.md          # API仕様書テンプレート
│   │   └── ui-spec.md           # UI仕様書テンプレート
│   ├── design/                  # ← 設計ドキュメント
│   │   ├── architecture.md
│   │   └── component-design.md
│   └── guides/                  # ← 各種ガイド
│       ├── github-setup.md
│       ├── ai-workflow.md
│       ├── test-plan.md
│       ├── acceptance-checklist.md
│       ├── operation-runbook.md
│       ├── release-notes.md
│       ├── retrospective.md
│       ├── onboarding-roadmap.md
│       └── checklists.md
├── tasks/
│   └── task-list.md             # ← タスク管理
├── .github/
│   ├── CODEOWNERS               # ← 領域別責任者
│   ├── ISSUE_TEMPLATE/
│   │   ├── feature-request.yml
│   │   ├── spec-change.yml
│   │   └── bug-report.yml
│   ├── PULL_REQUEST_TEMPLATE/
│   │   └── spec-change.md
│   ├── pull_request_template.md
│   └── workflows/
│       └── ci.yml               # ← CI パイプライン
├── CLAUDE.md                    # ← AI への指示（プロジェクト設定）
└── src/                         # ← 実装コード（プロジェクト固有）
```

---

## AI ツールの使い方

このハーネスは **Claude Code CLI**（ターミナル）および **GitHub Copilot / Codex**（VSCode）との連携を前提に設計されています。

```bash
# Claude Code CLI のセットアップ
npm install -g @anthropic-ai/claude-code
claude --version

# 仕様書を読み込んで実装依頼
claude "docs/spec/feature-spec.md を読んで、受入基準を全て満たす実装を行ってください"

# 仕様書のレビュー依頼
claude "docs/spec/feature-spec.md をレビューして、曖昧な点・矛盾・抜け漏れを指摘してください"
```

詳細は `docs/guides/ai-workflow.md` を参照してください。

---

## 導入ステップ

| ステップ | 期間 | アクション |
|---------|------|-----------|
| PHASE 1 | 当日〜3日 | テンプレートコピー・Constitution.md 初版・AI セットアップ |
| PHASE 2 | 1週間 | Branch Protection・CODEOWNERS・最初の仕様書 PR |
| PHASE 3 | 1ヶ月 | 仕様書ファースト習慣の定着・レトロスペクティブ開始 |
| PHASE 4 | 3ヶ月 | CI 自動化・メトリクス計測開始 |
| PHASE 5 | 6〜12ヶ月 | 全体展開・オンボーディング短縮確認 |

詳細は `docs/guides/onboarding-roadmap.md` を参照してください。

---

## ライセンス・クレジット

- ハーネスの手法は書籍「仕様駆動開発 実践入門 〜 AIで実現する開発方法論」に基づいています。
- テンプレートは MIT ライセンスで自由に使用・改変できます。
