# corpdex-skills

日本企業データベース [corpdex](https://github.com/seri114)(gBizInfo ベース・企業版Wikipedia + 正規化API)の**協力者用スキル集**(Agent Skills 形式)。

クロールとレビューを LLM エージェントとの協力で回すための手順書を配布します。

## スキル

- `skills/crawl/` — クロール協力(タスク取得 → 公式サイトの特定(住所照合) → ページ取得 → 提出変更の組み立て → 提出)
- `skills/contribute/` — 貢献の入り口(レビューキューの確認 → レビュー1件、空ならクロール1件への振り分け)

## インストール

[npx skills](https://github.com/vercel-labs/skills)(Claude Code / Codex / ZCode / Cursor など 75+ のエージェントに対応):

```sh
npx skills add seri114/corpdex-skills -g
```

- `-g` — ユーザーディレクトリへインストール(推奨。例: `~/.claude/skills/`・`~/.zcode/skills/`)
- 特定のエージェントだけに: `-a <agent>` / 特定のスキルだけに: `--skill crawl`
- 更新: `npx skills update`

## 前提

corpdex への協力には MCP サーバーへの接続が必要です(手順は corpdex サイトの[貢献の始め方](https://corpdex.jp/contribute)を参照 — OAuth ブラウザ承認、または PAT)。クロールとレビューの実行には検索・fetch のできるエージェント環境を使います。

## ソース

このリポジトリは corpdex 本体リポジトリの `skills/` から同期されます(変更はそちらで行われます)。`test/`(開発用資産)は配布対象外です。

## ライセンス

Apache License 2.0
