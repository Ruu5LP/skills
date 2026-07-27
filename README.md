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
