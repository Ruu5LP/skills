---
name: github-to-tickets
description: Breaks a plan, spec, or the current conversation into vertical-slice GitHub issues, each declaring which other issues block it, and publishes them via the gh CLI. Use whenever the user wants to turn a design/plan into trackable work items, says "make tickets for this", "break this into issues", or wants a big feature split into agent-sized chunks before implementation starts. Do NOT use for a single trivial change that doesn't need tracking, or for triaging existing issues (see the github-triage skill for that).
---

# GitHub To-Tickets

Break a plan, spec, or conversation into **tickets** — small vertical slices, each declaring the tickets that must complete before it can start — and publish them as GitHub issues.

Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (`skills/engineering/to-tickets`, MIT License, Copyright (c) 2026 Matt Pocock), simplified to publish straight to GitHub via `gh` instead of a configurable multi-tracker backend.

## Why vertical slices

A ticket should cut a narrow but *complete* path through every layer it touches (schema, API, UI, tests) rather than a horizontal slice of just one layer. Each slice should be demoable or verifiable on its own, and small enough to fit in a single focused work session. Do prefactoring (renames, extracting shared code) first, as its own ticket, so the feature tickets stay clean.

**Exception — wide refactors**: a mechanical change with a huge blast radius (renaming a widely-used symbol, retyping a shared field) can't be sliced vertically without breaking everything at once. Sequence these as expand → migrate (batched) → contract instead of forcing a tracer-bullet shape.

## Process

1. **Gather context** — use whatever's already in the conversation (the plan/spec/design just discussed). If the user references an existing issue or file, read it fully.
2. **Explore the codebase** if you haven't already, so ticket descriptions use real terminology from the project and respect existing patterns/decisions.
3. **Draft the tickets** — for each one, work out:
   - a short title
   - what it delivers, end-to-end, from the user's perspective (not a layer-by-layer implementation list)
   - which other draft tickets block it (or "none — can start immediately")
4. **Present the breakdown as a numbered list** before creating anything. Ask the user: does the granularity feel right (too coarse/fine)? Are the blocking edges correct? Iterate until approved — don't publish unapproved tickets.
5. **Publish to GitHub**, blockers first so later tickets can reference real issue numbers:

```bash
gh issue create --title "<title>" --body "$(cat <<'EOF'
## What to build
<end-to-end behavior, from the user's perspective>

## Acceptance criteria
- [ ] criterion 1
- [ ] criterion 2

## Blocked by
<"None — can start immediately", or "#12, #13">
EOF
)"
```

Apply a `ready-for-agent` label if the repo has one (check with `gh label list` first) — these tickets are meant to be pickup-ready by construction. If the source was an existing issue, add a `## Parent` section linking back to it.

Avoid embedding specific file paths or code snippets in ticket bodies — they go stale fast. Exception: if a prototype produced something that encodes a real decision more precisely than prose (a schema, a state machine, a type shape), inline the essential part and note it came from a prototype.

Don't close or modify the parent issue/spec this breakdown came from.
