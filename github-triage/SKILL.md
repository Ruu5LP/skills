---
name: github-triage
description: Moves GitHub issues and external PRs through a labeled triage state machine using the gh CLI — categorize (bug/enhancement), verify the claim, and either write an agent-ready brief, ask the reporter for more info, or close as wontfix. Use whenever the user says "triage the issues", "what needs my attention on GitHub", "let's look at #<number>", or wants to move an issue/PR toward a decision. Do NOT use for reviewing the actual code diff of a PR (that's a code review task) — this skill is about deciding *what state the issue/PR is in*, not judging code quality.
---

# GitHub Triage

Move issues (and external PRs, since a PR is an issue with attached code) through a small triage state machine, using `gh`.

Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (`skills/engineering/triage`, MIT License, Copyright (c) 2026 Matt Pocock), stripped of its dependency on his other skills and setup script so it works standalone with plain `gh`.

## Labels

Two **category** labels:
- `bug` — something is broken
- `enhancement` — new feature or improvement

Five **state** labels:
- `needs-triage` — not yet evaluated
- `needs-info` — waiting on the reporter for more information
- `ready-for-agent` — fully specified, ready for an agent (Claude Code, Codex, etc.) to pick up
- `ready-for-human` — needs human judgment/implementation
- `wontfix` — will not be actioned

Every triaged issue carries exactly one category label and one state label. If a repo doesn't have these labels yet, check with `gh label list` and offer to create the missing ones with `gh label create` before triaging (ask first — creating labels is a repo-visible change).

State transitions: unlabeled → `needs-triage` → one of `needs-info` / `ready-for-agent` / `ready-for-human` / `wontfix`. `needs-info` returns to `needs-triage` once the reporter replies. If labels look inconsistent (e.g. two state labels on one issue), flag it and ask before changing anything.

## Invocation

Interpret natural-language requests like "show me what needs attention", "let's look at #42", "move #42 to ready-for-agent". Always confirm label/comment/close actions before executing them — they're visible to anyone watching the repo.

## Show what needs attention

```bash
gh issue list --label needs-triage --state open
gh issue list --search "no:label" --state open
gh issue list --label needs-info --state open
gh pr list --search "no:label,needs-triage" --state open   # if external PRs are in scope
```

Present three buckets, oldest first: unlabeled, `needs-triage`, and `needs-info` issues with new activity since the last triage comment. Tag PRs with `[PR]`. Let the user pick what to work on.

## Triage a specific issue or PR

1. **Gather context** — `gh issue view <n> --comments` (or `gh pr view <n> --comments` plus `gh pr diff <n>` for a PR). Read prior triage notes so you don't re-ask resolved questions. Search the codebase for an existing implementation of the requested behavior — if it already exists, this is a `wontfix` (already implemented), not a rejection.
2. **Recommend** — state your category/state recommendation with reasoning, and whether it's already implemented. Wait for the user's direction rather than acting unilaterally.
3. **Verify the claim** — for a bug, try to reproduce it from the reporter's steps. For a PR, check it out and run the relevant tests. Report: confirmed (with the code path), failed to reproduce, or insufficient detail (a `needs-info` signal).
4. **Ask clarifying questions if needed** — if the request is underspecified, ask the reporter directly (via a `needs-info` comment) rather than guessing.
5. **Apply the outcome** (confirm with the user first):
   - `ready-for-agent` — post a brief comment: what to build, acceptance criteria, relevant files/context you found. Apply the label.
   - `ready-for-human` — same brief structure, but note why it needs a human (judgment call, external access, manual testing).
   - `needs-info` — post the template below, apply the label.
   - `wontfix` — close with a reason: already implemented (point to where), or a polite rejection explanation.

```bash
gh issue comment <n> --body "..."
gh issue edit <n> --add-label "ready-for-agent" --remove-label "needs-triage"
gh issue close <n> --comment "..."
```

## needs-info comment template

```markdown
## Triage Notes

**What we've established so far:**
- point 1

**What we still need from you:**
- specific, actionable question 1
```

## Quick override

If the user says "move #42 to ready-for-agent" directly, skip the verification steps, confirm the label change, and apply it.
