# Third-Party Notices

## github-triage, github-to-tickets, codebase-design, improve-codebase-architecture

Adapted from [mattpocock/skills](https://github.com/mattpocock/skills), licensed under the MIT License:

```
MIT License

Copyright (c) 2026 Matt Pocock

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

`github-triage` and `github-to-tickets` were rewritten to drop dependencies on Matt Pocock's other skills (`/grilling`, `/domain-modeling`) and his `/setup-matt-pocock-skills` configuration step, so they work standalone against plain `gh`. The core state-machine/vertical-slice ideas are his.

`codebase-design` is copied close to verbatim (it was already self-contained). `improve-codebase-architecture` had its grilling-loop step (step 3) rewritten to a plain conversation instead of invoking his separate `/grilling` and `/domain-modeling` skills, and its `CONTEXT.md`/ADR references made optional rather than assumed. The deep-module vocabulary, the HTML report design, and the architecture-scanning process are his.

## addyosmani/agent-skills

The following skills were copied from [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) and are distributed under its MIT License:

- `interview-me`
- `spec-driven-development`
- `planning-and-task-breakdown`
- `shipping-and-launch`

The upstream repository and its `LICENSE` file are the authoritative source for the license text. These skills were installed from the upstream default branch on 2026-08-03 after checking the current README and each selected `SKILL.md`.

## github/awesome-copilot

The following skills were copied from [github/awesome-copilot](https://github.com/github/awesome-copilot) and are distributed under its MIT License:

- `incident-postmortem`
- `impediment-prioritization` (including `references/scoring-rubric.md`)

The upstream repository and its `LICENSE` file are the authoritative source for the license text. These skills were installed from the upstream default branch on 2026-08-03 after checking the current README and each selected `SKILL.md`.

## deanpeters/Product-Manager-Skills

`prioritization-advisor` was copied from [deanpeters/Product-Manager-Skills](https://github.com/deanpeters/Product-Manager-Skills). It is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). The upstream repository and its license notice are the authoritative source for the full terms. This skill was installed from the upstream default branch on 2026-08-03 after checking the current README and `skills/prioritization-advisor/SKILL.md`.

## mino-code-design

Not copied from a third party — this repo's own skill, applying design principles publicly published by ミノ駆動 (Mino Driven), author of『良いコード/悪いコードで学ぶ設計入門』. Sources cited directly in [mino-code-design/SKILL.md](mino-code-design/SKILL.md).
