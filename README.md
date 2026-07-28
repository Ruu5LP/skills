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

`github-triage` と `github-to-tickets` は [mattpocock/skills](https://github.com/mattpocock/skills)（MIT License）の該当skillから、
自己完結して動くように書き直したもの。ライセンス表記は [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) 参照。

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
