---
name: improve-codebase-architecture
description: Scans a codebase for "deepening opportunities" — shallow modules that could become deep — and presents them as a visual HTML report ranked by recommendation strength. Use when the user wants an architecture review, asks "what's structurally wrong with this codebase", wants to find refactor targets before a big feature, or wants to make a part of the codebase more testable. Do NOT use for a quick one-off code review of a single diff (that's a lighter-weight task) — this is for standing back and looking at structure.
---

# Improve Codebase Architecture

Surface architectural friction and propose **deepening opportunities** — refactors that turn shallow modules into deep ones. The aim is testability and navigability.

Adapted from [mattpocock/skills](https://github.com/mattpocock/skills) (`skills/engineering/improve-codebase-architecture`, MIT License, Copyright (c) 2026 Matt Pocock), with the grilling-loop step simplified to a plain conversation instead of depending on his separate `/grilling` and `/domain-modeling` skills.

This skill is built on the shared design vocabulary in the **codebase-design** skill (**module**, **interface**, **depth**, **seam**, **adapter**, **leverage**, **locality**) and its principles (the deletion test, "the interface is the test surface", "one adapter = hypothetical seam, two = real"). Use these terms exactly in every suggestion — don't drift into "component," "service," "API," or "boundary."

If the project has a domain glossary (`CONTEXT.md`) or architecture decision records (`docs/adr/`), read them first and use their vocabulary/decisions — but don't require them to exist. Most projects won't have these; work without them.

## Process

### 1. Explore

**Scope before you scan — YAGNI.** Deepening a module pays off by making future changes to it easier, so put extra weight on the parts of the codebase that have recently changed. Decide *where* to look before you look:

- If the user named a direction — a module, a subsystem, a pain point — take it, and skip the inference below.
- Otherwise, walk back a good stretch of the commit history (`git log --oneline`) to find the codebase's hot spots — the files and areas that keep coming up — and let those paths pull your attention first. If the changes are scattered with no clear hot spot, widen the net.

If `CONTEXT.md` or `docs/adr/` exist, read them first for the area you're touching.

Then use the Agent tool with `subagent_type=Explore` to walk the codebase. Don't follow rigid heuristics — explore organically and note where you experience friction:

- Where does understanding one concept require bouncing between many small modules?
- Where are modules **shallow** — interface nearly as complex as the implementation?
- Where have pure functions been extracted just for testability, but the real bugs hide in how they're called (no **locality**)?
- Where do tightly-coupled modules leak across their seams?
- Which parts of the codebase are untested, or hard to test through their current interface?

Apply the **deletion test** to anything you suspect is shallow: would deleting it concentrate complexity, or just move it? A "yes, concentrates" is the signal you want.

### 2. Present candidates as an HTML report

Write a self-contained HTML file to the OS temp directory so nothing lands in the repo. Resolve the temp dir from `$TMPDIR`, falling back to `/tmp` (or `%TEMP%` on Windows), and write to `<tmpdir>/architecture-review-<timestamp>.html` so each run gets a fresh file. Open it for the user — `xdg-open <path>` on Linux, `open <path>` on macOS, `start <path>` on Windows — and tell them the absolute path.

The report uses **Tailwind via CDN** for layout and styling, and **Mermaid via CDN** for diagrams where a graph/flow/sequence reliably communicates the structure. Mix Mermaid with hand-crafted CSS/SVG visuals — use Mermaid when relationships are graph-shaped (call graphs, dependencies, sequences), and hand-built divs/SVG when you want something more editorial (mass diagrams, cross-sections, collapse animations). Each candidate gets a **before/after visualisation**. Be visual.

For each candidate, render a card with:

- **Files** — which files/modules are involved
- **Problem** — why the current architecture is causing friction
- **Solution** — plain English description of what would change
- **Benefits** — explained in terms of locality and leverage, and how tests would improve
- **Before / After diagram** — side-by-side, custom-drawn, illustrating the shallowness and the deepening
- **Recommendation strength** — one of `Strong`, `Worth exploring`, `Speculative`, rendered as a badge

End the report with a **Top recommendation** section: which candidate you'd tackle first and why.

**If `CONTEXT.md` exists, use its vocabulary for the domain** (e.g. "the Order intake module" rather than "the FooBarHandler"). Use the codebase-design vocabulary for the architecture regardless.

**ADR conflicts** (if `docs/adr/` exists): if a candidate contradicts an existing ADR, only surface it when the friction is real enough to warrant revisiting the ADR. Mark it clearly in the card (e.g. a warning callout: _"contradicts ADR-0007 — but worth reopening because…"_). Don't list every theoretical refactor an ADR forbids.

See [HTML-REPORT.md](HTML-REPORT.md) for the full HTML scaffold, diagram patterns, and styling guidance.

Do NOT propose interfaces yet. After the file is written, ask the user: "Which of these would you like to explore?"

### 3. Talk through the chosen candidate

Once the user picks a candidate, walk through it conversationally — constraints, dependencies, the shape of the deepened module, what sits behind the seam, what tests survive. Ask questions rather than assuming; this is a design decision, not a mechanical refactor.

As decisions crystallize:

- **If the project keeps a `CONTEXT.md`** and you're naming a deepened module after a concept not yet in it, offer to add the term. Don't create the file unprompted if the project doesn't already have one.
- **If the user rejects the candidate with a load-bearing reason** and the project keeps ADRs, offer: _"Want me to record this as an ADR so future architecture reviews don't re-suggest it?"_ Only offer when the reason would actually matter to a future reviewer — skip ephemeral reasons ("not worth it right now").
- **Want to explore alternative interfaces for the deepened module?** Use the **codebase-design** skill's Design It Twice pattern (parallel sub-agents proposing radically different interfaces, then compared).
