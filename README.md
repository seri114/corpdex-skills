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

corpdex への協力には corpdex サイトのアカウントとアクセストークン(PAT)が必要です(サイトのマイページ・協力者向け案内を参照)。現在のスキルは corpdex CLI(サイトの配布ページから入手)前提の手順です。MCP サーバー経由の協力導線を準備中で、公開された時点でスキルとこの README を更新します。

## ソース

このリポジトリは corpdex 本体リポジトリの `skills/` から同期されます(変更はそちらで行われます)。`test/`(開発用資産)は配布対象外です。

## ライセンス

Apache License 2.0
