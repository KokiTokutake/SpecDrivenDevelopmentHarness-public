# ガイド（Guides）ディレクトリ

SDD（仕様駆動開発）の各工程を実践するための**運用ガイド集**です。
チームへの導入・日常運用・ツール設定など、工程横断的な手順書を格納します。

## ファイル一覧

| ファイル | 内容 | 参照タイミング |
|---------|------|-------------|
| `onboarding-roadmap.md` | SDD 導入ロードマップ（PHASE 1〜5） | プロジェクト開始時 |
| `github-setup.md` | GitHub リポジトリ設定手順 | PHASE 1〜2（初期設定時） |
| `ai-workflow.md` | AI ツール（Claude Code 等）活用ワークフロー | 開発者オンボーディング時 |
| `checklists.md` | 工程別チェックリスト集 | 各工程の開始・完了確認時 |
| `acceptance-checklist.md` | 受入確認チェックリスト | 検証・受入工程（Verify/Accept） |
| `test-plan.md` | テスト計画テンプレート | 実装工程開始前 |
| `release-notes.md` | リリースノートテンプレート | 移行・運用工程（Migration/Operation） |
| `retrospective.md` | レトロスペクティブ進行ガイド | スプリント終了時 |
| `operation-runbook.md` | 運用ランブック（障害対応・定期メンテ） | 運用工程・インシデント時 |

## ガイドと工程の対応

```
工程                ガイド
────────────────────────────────────────────
原則決定工程    →   (Constitution.md を直接編集)
企画・要件定義  →   checklists.md（仕様レビュー項目）
設計計画工程    →   ai-workflow.md（AI 支援設計）
タスク分割工程  →   checklists.md（タスク分割チェック）
実装工程        →   ai-workflow.md、test-plan.md
検証・受入工程  →   acceptance-checklist.md
移行・運用工程  →   release-notes.md、operation-runbook.md
（全工程）       →   onboarding-roadmap.md、github-setup.md
（スプリント終了）→   retrospective.md
```

## ガイドを追加するときのルール

- **ファイル名**: `[内容を表す kebab-case].md`
- **対象読者を明記**: ガイドの冒頭に想定読者・前提条件を書く
- **工程との紐付け**: どの工程・タイミングで参照するかを明示する
- **手順は番号リスト**: 再現性のある手順は番号付きリストで記述する
- **スクリーンショット**: `guides/images/` に保存し相対パスで参照する

## 仕様書・設計書との違い

| ドキュメント種別 | 目的 | 更新頻度 |
|----------------|------|---------|
| 仕様書（`docs/spec/`） | 何を作るかの合意 | 要件変更時 |
| 設計書（`docs/design/`） | どう作るかの設計 | 設計変更時 |
| **ガイド（`docs/guides/`）** | **どう進めるかの手順** | プロセス改善時 |