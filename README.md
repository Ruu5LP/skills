# skills

個人用のClaude Code / Codex 両対応skillリポジトリ。

このリポジトリでは、エンジニアリングプロジェクトマネジメント向けのSkillを、グローバルではなくリポジトリ直下で管理する。Codexでこのリポジトリを開くと、直下の各Skill（`<skill-name>/SKILL.md`）をこのプロジェクトのSkillとして利用できる。今回の追加では `~/.codex/skills` や `~/.claude/skills` へのコピー・シンボリックリンク作成は行っていない。

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
| `interview-me` | 曖昧な要望を一問ずつ確認し、目的・制約・成功指標を明らかにする |
| `spec-driven-development` | 実装前に仕様、前提、境界、テスト方針を作成する |
| `planning-and-task-breakdown` | 仕様を依存関係付きの実装可能なタスクへ分解する |
| `shipping-and-launch` | テスト、監視、段階展開、ロールバックを含むリリース計画を作る |
| `prioritization-advisor` | 状況に合う優先順位付けフレームワークを選ぶ |
| `reviewing-meetings` | 文字起こしから会議進行と本人の発言を振り返る |
| `incident-postmortem` | 障害の影響・時系列・根本原因・再発防止策を整理する |
| `impediment-prioritization` | ブロッカー、リスク、改善項目を価値・コスト・リスクで順位付けする |

`github-triage` / `github-to-tickets` / `codebase-design` / `improve-codebase-architecture` は [mattpocock/skills](https://github.com/mattpocock/skills)（MIT License）の該当skillから取り込んだもの（一部は自己完結するよう書き直し）。
`mino-code-design` はミノ駆動氏の公開資料に基づくこのリポジトリ独自のskill。ライセンス表記・出典は [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) 参照。

`mino-code-design`（命名・関数/クラスレベル）と `codebase-design` + `improve-codebase-architecture`（モジュール/インターフェースレベル）はミクロ⇔マクロで補完関係にある。

PM運用の標準ルール、Issueの項目、受入条件、リスク・リリース・振り返りのテンプレートは [AGENTS.md](AGENTS.md) と [docs/pm-workflow.md](docs/pm-workflow.md) にまとめている。外部待ち・社内判断待ち・仕様待ちを区別し、受入条件が確認されるまで完了扱いにしない。

### 今回追加した外部Skill

以下は、各 upstream の最新 `SKILL.md` を確認したうえで、このリポジトリ直下に配置した。出典とライセンスは [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) に記録している。

- [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills): `interview-me`, `spec-driven-development`, `planning-and-task-breakdown`, `shipping-and-launch`（MIT）
- [deanpeters/Product-Manager-Skills](https://github.com/deanpeters/Product-Manager-Skills): `prioritization-advisor`（CC BY-NC-SA 4.0）
- [github/awesome-copilot](https://github.com/github/awesome-copilot): `incident-postmortem`, `impediment-prioritization`（MIT）

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
