---
name: git-branch-pr
description: Automates the git workflow of creating a feature branch, committing changes, pushing, and opening a GitHub PR via the gh CLI, with an AI-generated branch name, commit message, and PR title/description based on the actual diff. Use this whenever the user wants to "cut a branch and open a PR", "ship this change", "commit and push and make a PR", or similar end-to-end git/PR requests — even if they only say "PR this" or "branch this out" without spelling out every step. Do NOT use it for reviewing existing PRs, resolving merge conflicts, or any task that isn't about creating a brand-new branch/commit/PR from the current working tree changes.
---

# Git Branch → PR Automation

Turns the current working-tree changes into a pushed branch and an open PR, using `gh` and `git`. Handles branch naming, commit message, and PR title/description generation from the actual diff instead of asking the user to write them.

## Why this exists

This is a fixed, low-judgment sequence (branch → commit → push → PR) except for two spots that genuinely benefit from reading the diff: naming the branch/commit well, and writing a PR description that actually reflects what changed. Do the mechanical steps deterministically with git/gh; use judgment only for the text generation.

## Preconditions

1. Confirm we're inside a git repository (`git rev-parse --is-inside-work-tree`). If not, stop and tell the user.
2. Confirm `gh` is installed and authenticated (`gh auth status`). If not, tell the user how to fix it (`gh auth login`) and stop.
3. Run `git status` to see the current branch and working-tree state. **Never run destructive commands** (`git reset --hard`, `git clean`, `git checkout --`) as part of this workflow — if the tree is in a confusing state (e.g. mid-rebase, detached HEAD), stop and describe what you see instead of guessing.

## Workflow

### 1. Determine the branch
- If the current branch is `main`/`master` (or the repo's default branch — check `git symbolic-ref refs/remotes/origin/HEAD` if unsure), a new branch is needed.
- If already on a non-default branch, ask the user whether to keep using it or cut a new one — don't assume.
- Generate the branch name from the diff content (staged + unstaged + untracked), not by asking the user. Use the pattern `type/short-kebab-slug` (e.g. `fix/null-check-auth`, `feat/csv-export`), inferring `type` (feat/fix/chore/refactor/docs/test) from what actually changed. Branch names are ASCII regardless of the repo's commit-message language.
- Create it with `git checkout -b <name>`.

### 2. Commit
- Look at `git status` and `git diff` (staged and unstaged) to see what needs committing. Stage specific files by name (`git add <file> <file>`) — never `git add -A`/`git add .` blindly, since that can sweep in files the user didn't intend (secrets, build artifacts, unrelated WIP). Show the user what you're about to stage if anything looks unexpected.
- Check `git log -5 --oneline` on the repo to match its existing commit message language and style (some repos here use Japanese, some English — follow the local convention rather than defaulting to one).
- Write a commit message that explains *why*, not just *what*, per the repo's usual terseness.
- If there's nothing to commit (tree already clean) but the branch has unpushed commits, skip straight to push.

### 3. Push and open the PR — confirm first
Pushing and opening a PR are both actions with external visibility, so **before running `git push` or `gh pr create`, show the user**:
- the branch name
- the commit message(s) that will go up
- the PR title and description you plan to use (generate the description from `git diff` against the base branch — summarize the actual change, don't just restate the commit message)

Wait for explicit confirmation. Once confirmed:
```bash
git push -u origin <branch-name>
gh pr create --title "<title>" --body "<body>" --base <default-branch>
```
Always create a regular PR (not `--draft`) unless the user asks otherwise for this run.

### 4. Report back
Print the PR URL that `gh pr create` returns so the user can click through.

## Notes
- If `gh pr create` fails because a PR already exists for the branch, surface the existing PR URL (`gh pr view --web` or the error output) instead of retrying blindly.
- If the user only wants some of the changes committed (not everything in the working tree), ask which files/hunks belong in this PR rather than assuming the whole diff goes together.
