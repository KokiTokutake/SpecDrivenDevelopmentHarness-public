# CLAUDE.md

This file provides guidance to Claude Code when working with code in this repository.

## プロジェクト概要

<!-- README.md の概要を簡潔に記載する -->
[プロジェクト名]: [1〜2行でプロジェクトの目的を説明]

## 仕様書・ドキュメントの場所

| ドキュメント | パス | 説明 |
|-------------|------|------|
| プロジェクト憲章 | `.constitution/Constitution.md` | 原則・制約・用語集（変更禁止） |
| 仕様書 | `docs/spec/` | 機能仕様・API仕様・UI仕様 |
| 設計書 | `docs/design/` | アーキテクチャ・コンポーネント設計 |
| タスク一覧 | `tasks/` | 実装タスクと進捗 |

> **実装前に必ず仕様書（status: approved）を確認すること。**

## 実装開始の必須手順（ゲートルール）

> **これらのルールは「推奨」ではなく「禁止事項」である。**

1. **実装開始前に必ず `/task-start [TASK-ID]` を実行する。**
   - このスキルを経由しない実装開始は禁止。
   - `/task-start` がブランチを作成するまで、コードに一切触れてはならない。

2. **mainブランチへの直接実装・コミットは禁止。**
   - `git branch --show-current` の結果が `main` のとき、コードを書いたり編集したりしてはならない。
   - ユーザーが「はい」「続けて」と言っても、ブランチが存在しなければ実装を始めてはならない。

3. **実装完了後は必ず `/task-done [TASK-ID]` を実行してPRを作成する。**
   - PRなしにタスクを「done」にしてはならない。

4. **ブランチ命名規則（Issue番号がある場合・ない場合どちらも必須）:**
   ```
   Issue番号あり: feat/[issue番号]-[タスク概要]
   Issue番号なし: feat/T-[タスク番号]-[タスク概要]  例: feat/T-012-user-skill-api
   ```

## 技術スタック

<!-- プロジェクトの主要技術を記載 -->
- **言語**: [TypeScript / Python / Go 等]
- **フレームワーク**: [Next.js / FastAPI / Gin 等]
- **DB**: [PostgreSQL / MySQL / MongoDB 等]
- **テスト**: [Jest / pytest / go test 等]
- **CI/CD**: [GitHub Actions]

## よく使うコマンド

```bash
# 開発サーバー起動
[npm run dev / python main.py 等]

# テスト実行
[npm test / pytest / go test ./... 等]

# テスト（単一ファイル）
[npm test -- --testPathPattern=xxx / pytest tests/xxx_test.py 等]

# リント・フォーマット
[npm run lint / ruff check . 等]

# ビルド
[npm run build / go build ./... 等]
```

## コーディングルール

### 必須ルール（Constitution.md より）
- 仕様書の受入基準をすべて満たしてから PR を出す
- 仕様書に書かれていない機能を勝手に追加しない
- テストは実装とセットで書く
- AI 生成コードは必ず人間がレビューする

### コーディングスタイル
- **命名**: [例: キャメルケース（関数）/ パスカルケース（クラス）]
- **コメント**: Why が自明でない場合のみ書く。What はコードで表現する
- **エラーハンドリング**: [例: Result 型 / 例外 / エラーコード]

## ディレクトリ構造

```
[プロジェクト固有の構造を記載]
src/
├── [モジュール名]/
│   ├── [ファイル名].[拡張子]
│   └── ...
└── ...
```

## PR・ブランチの規則

```bash
# ブランチ命名
feat/[issue番号]-[機能名]    # 機能追加
fix/[issue番号]-[内容]       # バグ修正
spec/[issue番号]-[機能名]    # 仕様書変更
docs/[issue番号]-[内容]      # ドキュメント

# コミットメッセージ
feat: [内容]（#[issue番号]）
fix: [内容]（#[issue番号]）
```

## 仕様変更が必要と判断した場合

実装中に仕様の不備・矛盾を発見した場合：

1. 勝手に解釈して実装しない
2. Issue にコメントで報告する（`question` ラベル）
3. 仕様書オーナーの判断を待つ
4. 承認後に仕様書を更新し、それから実装する

## 用語集

<!-- .constitution/Constitution.md の用語集と同期する -->
| 用語 | 定義 |
|------|------|
| [用語] | [定義] |
