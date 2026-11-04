# Design Out Loud

*System design in 90 sittings.*

**[Read the course →](https://reasonfrom.com/courses/design-out-loud/)**

Read it in the browser, like a book. You do not clone this repository and you do not fork it. Start at Day 1. One sitting, 30–40 minutes. Attempt before you read. Stop at 40.

If that link is not live yet, GitHub Pages still needs to be turned on for this repository (once, from `main`). The book is the way to study. The files below are the same pages.

## The promise

You learn to run a 35–45 minute design interview out loud. Requirements come before boxes. Every number has an assumption you can recompute. The diagram is one you can redraw from memory. The trade-off names what you gave up, and what breaks at 10×. When a dependency dies, you say what the user sees.

Phase 1 installs that hour on one system: a pastebin. You do not leave the week with a catalog of caches, queues, and consensus. You leave able to take a one-line problem and finish a design a senior interviewer can push on.

There is no certificate and no cohort. The design log is the artifact. It stays in your notes. This site does not record progress.

## Who it is for

Engineers with 10 or more years of experience, preparing for senior and staff system design interviews, or for the design reviews those interviews imitate. The altitude is someone who has already shipped, and now has to make the reasoning visible on a whiteboard.

Not a new-grad course. Not an AI or ML course. The 90 days are distributed systems, scale, and reliability only.

## Phase status

| Days | Status |
|---|---|
| 1–7 | Written. Phase 1, the pastebin method. [Start Day 1 in the book](https://reasonfrom.com/courses/design-out-loud/day-01/). |
| 8–28 | Written. Phase 2, one box at a time on that pastebin. Day 28 is a different mock. |
| 29–49 | Written. Phase 3, guarantees, keys, and the failure you accept. Day 49 is a different mock. |
| 50–77 | Written. Phase 4, product-shaped systems. Days 63 and 77 are different mocks.
| 78–84 | Written. Phase 5, failure and senior signal. Day 84 is a dispatch mock with a late failure.
| 85–90 | Written. Phase 6, mocks and the kit hookup. Days 86, 88, and 90 are closed-book mocks. |

Phase 1 is the spine. Phase 2 adds a box only when that pastebin misses a number or a fault you already stated. Do not skip ahead to "look distributed."

The course brief is [PROPOSAL.md](PROPOSAL.md). Locked choices (audience, length, no cohort, kit sold separately, no certificate) live there and in this file. Do not reopen them inside a lesson.

## The kit is sold separately

The interview kit (timer script, requirements checklist, estimation sheet, stencils, trade-off card, follow-up bank, grader notes, extra problem cards) is a **separate product**. It is not in this repo, and it is not required to start.

Each written lesson leaves a single hookup note: one checklist row or one stencil callout, so the kit can attach later to the same places you already practice. Days 1–84 do not need it. [Day 85](days/85-kit-hookup-and-the-design-log.md) shows the attachment. Day 90 can be run without the kit, on the [multiplayer lobby](prompts/multiplayer-lobby.md) fallback.

Do not block the other days waiting for the kit.

## For contributors

Students should use the book site, not a local checkout.

To change the course: clone this repository, branch from `main`, and open a pull request. Lesson pages live in `days/`. The site is Docsify at the repository root (`index.html`, `_sidebar.md`, `.nojekyll`). GitHub Pages should deploy from branch `main` and folder `/` (root), not `/docs`.

```
days/            one file per day; Phases 1–5 are days 01–84
prompts/         problem bank (folder name stays `prompts/`; pastebin is day 7; image upload is day 28; warehouse inventory is day 49; job scheduler is day 63; email inbox is day 77)
design-log/      template only; real notes stay private and are not pushed
stencils/        the six diagram types, blank
appendices/      optional notes after day 90: company quirks, Dynamo, Spanner, Raft
CURRICULUM.md    the 90-day map
PROPOSAL.md      the brief this repo is built from
START.md         book homepage
HOW-TO-STUDY.md  the daily ritual
CHECKLIST.md     printable Phase 1–4 list; not shared state
LICENSE          MIT
```

Pages under `prompts/` are the problem bank. The directory name stays `prompts/` so links keep working. If a problem file changes, it wins over a title in the curriculum. Read the problem page on the site before a mock. Do not keep a private copy as the source of truth.

## License

[MIT](LICENSE). Copyright (c) 2026 Arunveer Singh.
