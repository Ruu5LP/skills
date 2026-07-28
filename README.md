# skills

個人用のClaude Code / Codex 両対応skillリポジトリ。

## 使い方（新しいPCでのセットアップ）

```bash
git clone <this-repo> ~/Documents/GitHub/skills

for skill in ~/Documents/GitHub/skills/*/; do
  name=$(basename "$skill")
  [ "$name" = ".git" ] && continue
  ln -s "$skill" ~/.claude/skills/"$name"
  ln -s "$skill" ~/.codex/skills/"$name"
done
```

- `~/.claude/skills/<name>` と `~/.codex/skills/<name>` は、このリポジトリ内の各skillフォルダへのシンボリックリンク。
- 両CLIとも `SKILL.md` を同じフォーマットで読むので、1つのskillをそのまま共有できる。
- `~/.claude/skills/` や `~/.codex/skills/` にある他のローカル専用skill（このリポジトリで管理していないもの）とは共存する。

## 新しいskillを追加するとき

1. `<skill-name>/SKILL.md` をこのリポジトリ直下に作る
2. 上と同じ要領で `~/.claude/skills/<skill-name>` と `~/.codex/skills/<skill-name>` にシンボリックリンクを張る
3. commit & push

## Skill一覧

| skill | 内容 |
|---|---|
| `git-branch-pr` | ブランチ作成→コミット→push→PR作成を自動化（オリジナル） |
| `github-triage` | GitHub Issue/PRをラベルのステートマシンで triage |
| `github-to-tickets` | 会話やプランをブロッキング関係付きのGitHub Issueに分解 |
| `mino-code-design` | 命名・責務肥大化・条件分岐・抽象化の罠を防ぐコード設計チェック（オリジナル、ミノ駆動氏の資料ベース） |
| `codebase-design` | 「深いモジュール」設計の語彙・原則（interface/seam/depth/leverage/locality） |
| `improve-codebase-architecture` | コードベースをスキャンして「浅いモジュール」をビジュアルHTMLレポートで指摘 |

`github-triage` / `github-to-tickets` / `codebase-design` / `improve-codebase-architecture` は [mattpocock/skills](https://github.com/mattpocock/skills)（MIT License）の該当skillから取り込んだもの（一部は自己完結するよう書き直し）。
`mino-code-design` はミノ駆動氏の公開資料に基づくこのリポジトリ独自のskill。ライセンス表記・出典は [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) 参照。

`mino-code-design`（命名・関数/クラスレベル）と `codebase-design` + `improve-codebase-architecture`（モジュール/インターフェースレベル）はミクロ⇔マクロで補完関係にある。

## 外部ツール（このリポジトリには含めない）

自作じゃないフル機能のフレームワークは、このリポジトリに取り込まず各CLIのplugin機構でそのままインストールする。
理由: [obra/superpowers](https://github.com/obra/superpowers) はSKILL.mdだけでなく自動発火用のhookも含めて動くため、
skillフォルダだけコピーするとその挙動が失われる。フル機能を活かすため公式plugin配布のまま使う。

- **[Superpowers](https://github.com/obra/superpowers)** — 開発方法論（brainstorm→plan→TDD→subagent実行）。手戻りコストが高い/規模の大きいプロジェクトで使う。
  ```
  /plugin marketplace add obra/superpowers-marketplace
  /plugin install superpowers@superpowers-marketplace
  ```
  判断基準は [[dev_methodology_by_project_weight]] 参照（AiRuu Inc.リポジトリのメモリ）。「〜作りたい」と言ったらデフォルトでこちらを使う。本当に軽いタスクだけこのリポジトリのオリジナルskillで完結させる。

- **[vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills)** — Vercel公式。React/Next.jsのパフォーマンスルール集（`react-best-practices`）、UI/UXレビュー（`web-design-guidelines`）など。
  **LICENSEファイルがなく著作権はVercelに留保されている**ため、`github-triage`/`github-to-tickets`のように中身をコピー・書き換えて取り込むことはしない。Reactプロジェクトごとに公式インストーラーで直接入れる。
  ```
  npx skills add vercel-labs/agent-skills
  ```
