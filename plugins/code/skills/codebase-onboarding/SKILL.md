---
name: codebase-onboarding
description: Generate a fresh, guided onboarding course for a new developer joining this codebase — setup, architecture, testing, patterns, and frameworks — as short interactive lessons with quizzes.
disable-model-invocation: true
argument-hint: "optional: subdirectory or package to scope onboarding to (defaults to the whole repo)"
---

The user is new to this codebase (or onboarding someone who is) and wants a guided course through it: how to set it up and run it, how it's put together, how it's tested, what patterns it leans on, and what frameworks/libraries it uses. Unlike a wiki page, this course is not meant to be maintained — it's regenerated fresh from the codebase's current state every time it's run, so it never goes stale in the way hand-written onboarding docs do.

## Scoping the Onboarding

Resolve what to scan before anything else:

- No argument: the whole repository.
- A subdirectory or package name: scope the scan (and every module below) to that path — useful for a monorepo where only one plugin/package/service is relevant to the new hire's role.

Read whatever ground-truth documentation already exists and treat it as authoritative — `README.md`, `CLAUDE.md`/`AGENTS.md`, `CONTRIBUTING.md`, and any `docs/` directory. Supplement only where these are silent or out of date; don't re-derive from scratch what's already correctly documented. If this repository's own setup instructions are given verbatim somewhere (e.g. exact `pnpm`/`npm`/`make` commands), reuse them verbatim rather than reconstructing your own guess at the same commands.

## Resolving Paths

This skill ships alongside files it references with a bare `./` — `./OVERVIEW-FORMAT.md`, `./GLOSSARY-FORMAT.md`, and `./RESOURCES-FORMAT.md` live next to `SKILL.md` in the installed skill, and should resolve there.

`./.onboarding-docs/` and everything under it means the opposite: it must resolve against the root of the scope being onboarded (the repo root, or the given subdirectory), never against this skill's own install directory. If there's any ambiguity about which repository or path that is, confirm it with the user before writing anything.

## What Counts As A Topic

A topic is a technology, technique, architectural pattern, or codebase-specific convention a new engineer needs to recognize to work productively here. This is scanned from the codebase's current state, not a diff — but the filter is the same spirit as reviewing a PR: ask "what would trip someone up or leave them stuck if nobody explained it?"

Unlike a PR-scoped review, this includes the codebase's *own* conceptual vocabulary — not just external technology. If this repository has invented names for its own concepts (a "skill," a "widget," a "plugin manifest," a service boundary with a specific name), that counts as a topic too, because a new hire has no way to look it up elsewhere.

What still doesn't count: the business/product domain — what the company sells, why a feature exists, product rationale. That's onboarding to the *business*, not the *codebase*, and belongs in a different document if it exists at all.

## Discovering Modules

Five modules are always covered, sourced like this:

- **Setup & Local Development** — install/run/build commands, pulled from README/CLAUDE.md/AGENTS.md and package manifest scripts. Link out to docs for any non-trivial tool involved (e.g. the package manager's workspace feature, a codegen step).
- **Architecture** — derived from documented structure plus an actual scan of the directory layout and how the pieces connect. Name any recognizable architectural pattern present (monorepo workspace, plugin/manifest system, layered service, event-driven pipeline, etc.) and link to a resource on that named pattern specifically — not a generic architecture primer.
- **Testing** — detect the test framework(s) and config in use, how to run them, and what kinds of tests exist (unit/integration/e2e) and where they live.
- **Patterns** — recurring idioms for error handling and async/data flow, found by reading representative code (not just config) — e.g. a consistent `Result`-style return type, a particular async/await convention, a shared error-boundary pattern.
- **Frameworks & Libraries** — architecturally significant dependencies only: the framework itself and libraries that materially shape how code is written here. Skip minor/utility dependencies even if they appear in the manifest — a lesson on a date-formatting helper isn't worth a new hire's time. Each significant dependency gets a link to its own official docs.

Beyond these five, scan for anything else notable a new engineer would otherwise stumble on unexplained — a CI/CD pipeline, a deployment mechanism, a bespoke internal script or tool — and add it as its own module. Every discovered module is subject to the course-size budget below; don't add one just because it exists.

## Course Size Budget

Aim for roughly **10–15 lessons total**, across all modules combined, completable in a workday. This is a soft target with teeth: when the mandatory modules plus discovered extras would produce more than that, prioritize by "would a new engineer actually be stuck without this" and drop or merge the lowest-value topics rather than covering everything found. A short, tightly-curated course beats an exhaustive one nobody finishes.

## Onboarding Workspace

Treat `./.onboarding-docs/`, resolved against the scope root (see [Resolving Paths](#resolving-paths)), as the workspace for this course. Every run wipes and fully regenerates it — there is no drift detection, no incremental extension, and no reuse of a prior run's content. If the codebase has changed, rerunning the skill is the update mechanism.

- `OVERVIEW.md`: what this codebase (or scoped path) is, the modules this course covers, and what success looks like. Auto-drafted from the scan, then confirmed with the user (see [Overview Confirmation](#overview-confirmation)). Use the format in [OVERVIEW-FORMAT.md](./OVERVIEW-FORMAT.md).
- `GLOSSARY.md`: the canonical terms for this course — technical mechanisms and this codebase's own naming conventions alike. Use the format in [GLOSSARY-FORMAT.md](./GLOSSARY-FORMAT.md).
- `RESOURCES.md`: trusted sources backing every lesson, plus the repo's own docs as primary sources. Use the format in [RESOURCES-FORMAT.md](./RESOURCES-FORMAT.md).
- `NOTES.md`: a scratchpad for anything the learner tells you about their prior experience or preferences, plus the commit SHA this snapshot was generated from (for provenance only — nothing reads this to detect drift).
- `./site/`: the rendered course — a Varde-styled, multi-page site generated entirely by the `varde-docs` skill (see [Building the Site](#building-the-site)). **Never hand-write HTML into this directory** — it is regenerated in full each run. It contains:
  - `index.html`: the rendered Overview (the site's entry point).
  - `glossary.html`: the rendered Glossary.
  - `./lessons/*.html`: one file per lesson, titled `0001-<dash-case-name>.html`, numbered globally across all modules in teaching order.
  - `./reference/*.html`: compressed reference materials, sharing the same shell/typography as lessons but excluded from the sidebar (see [Reference Documents](#reference-documents)).
  - `./site/assets/*`: the one part of `./site/` `varde-docs` must never touch — hand-authored interactive behavior only, same restriction as in `teach-pr`.

Tell the user once, the first time you create this directory, that they should add `.onboarding-docs/` to their `.gitignore`. Don't edit the file yourself — it's their repo, not the workspace's.

## Overview Confirmation

Draft `OVERVIEW.md` yourself from the repo scan — scope, the modules it will cover (including anything discovered beyond the mandatory five), and what success looks like — then present it to the user before drafting any lesson content. This is the one checkpoint where a wrong scope or an irrelevant discovered module gets caught before the expensive part of the work runs.

If the person confirming is the learner themselves (the default case — see below), use this step to also gather their stated prior experience or preferences and record it in `OVERVIEW.md`'s Constraints and in `NOTES.md`. If the skill is being run by someone preparing material for a learner who isn't present, leave Constraints generic rather than guessing at a specific person's background.

Assume the invoking user is the learner unless they say otherwise — this skill is designed primarily for self-onboarding (a new hire running it on their own first day), not for someone else preparing material in advance, though nothing stops that use.

## Secrets Guardrail

Codebases (especially early-stage or misconfigured ones) can contain real secrets in `.env` files, config examples, or comments. Never quote an actual secret or credential value — an API key, token, password, connection string — anywhere in `OVERVIEW.md`, `GLOSSARY.md`, `RESOURCES.md`, a lesson, or a reference document, even if you found it verbatim in the repo. Describe its existence and where it's used instead: "an API key loaded from `.env`, validated in `src/auth/config.ts`," never the key itself.

## Philosophy

Same three pillars as any deep learning experience:

- **Knowledge**, from high-quality, high-trust resources — official docs, a maintainer's own writing, and this repo's own README/CLAUDE.md/CONTRIBUTING as primary sources.
- **Skills**, through short interactive lessons with a tight feedback loop (quizzes, guided reading of the actual code).
- **Wisdom**, from communities the learner can turn to once they're past the basics.

For every topic, search for a deep-dive resource on that specific mechanism or pattern before writing its lesson — official documentation or a well-known, credited source, never a shallow intro when a deep-dive exists. Add what you find to `RESOURCES.md`. Design lessons for long-term retention (retrieval practice, not just fluency): a quiz the learner has to actually recall the answer for, not one they can pattern-match from formatting.

## Lessons

The lesson is the unit of teaching: one tightly-scoped thing, numbered globally `0001-<dash-case-name>` across *all* modules (not reset per module) so the whole course reads as one sequence. Draft each lesson's content yourself — explanation, citations, quiz, exercise, and the links it needs to other lessons/glossary/reference docs — but never hand-author the HTML; that's `varde-docs`'s job, invoked once for the full batch (see [Building the Site](#building-the-site)).

Each lesson should be short and give one tangible win. Every quiz answer should be the same rough length so formatting doesn't leak the answer. Citations to this repo's own code use **stable anchors** — file path, module name, function/symbol name — never exact line numbers. Unlike a merged PR's frozen diff, this codebase keeps changing after the course is generated, and a line-number citation can point at the wrong thing within days.

Each lesson's practice step should send the learner back to the real file or command, not a paraphrase of it, and should remind them they can ask the agent follow-up questions — the agent is their teacher for anything unclear.

## Zone Of Proximal Development

Plan the entire lesson set — across every module — before generating any of it:

1. Break each confirmed module into the distinct things it needs to teach, filtered by [What Counts As A Topic](#what-counts-as-a-topic) and the [course size budget](#course-size-budget).
2. Order the full list by dependency and difficulty, not module order — setup naturally comes first; something that assumes familiarity with the architecture comes after the architecture lesson, regardless of which module either belongs to.
3. Generate a lesson for every item in the ordered list before stopping. Don't draft one lesson and pause — the learner should come back to a finished, ordered, fully linked course.

## Building the Site

`OVERVIEW.md`, `GLOSSARY.md`, and every lesson's drafted content are the editable sources of truth — the learner reads a polished, linked site, not raw markdown. That site is always built by invoking the sibling **`varde-docs`** skill; never hand-author any HTML yourself.

Invoke it exactly once, after `OVERVIEW.md` is confirmed, `GLOSSARY.md` is drafted, and every lesson in the full ordered set has its content drafted. Never invoke it per-lesson or per-module.

Since `varde-docs` takes free-form instructions rather than a fixed schema, spell out explicitly in the request you hand it:

- **Output path**: `.onboarding-docs/site/` (resolved per [Resolving Paths](#resolving-paths)), overwriting `index.html`, `glossary.html`, and everything under `lessons/` and `reference/` — but never touching `./site/assets/`.
- **Exact filenames, given verbatim** — `index.html` for the rendered Overview, `glossary.html` for the rendered Glossary, `lessons/0001-<dash-case-name>.html` etc. for each lesson in global teaching order. Don't let `varde-docs` invent kebab-case names from titles; the numeric prefixes encode teaching order.
- **A sidebar grouped by module, in the confirmed module order, with progression numbering**: each module section header is prefixed with its position in that order (`1. Setup & Local Development`, `2. Architecture`, …), and each lesson underneath is prefixed with its *global* lesson number from its filename, not a per-section restart (`1. Installing dependencies`, `2. Running the dev server`, …, continuing across module boundaries) — a lesson's number in the sidebar always matches its `NNNN-` filename prefix, so the learner can see overall progress through the course even though lessons are grouped by module rather than listed in one flat sequence. This is followed by a final, unnumbered section with exactly two items — "Overview" → `index.html` and "Glossary" → `glossary.html`. (This differs from `teach-pr`'s fixed two-section sidebar; grouping by module is what makes a multi-module course navigable.) Reference pages under `reference/` stay out of the sidebar entirely, reachable only via inline links from lessons that cite them.
- **The content itself** — the Overview, the Glossary, and every lesson's drafted content (including the specific links each lesson needs), passed through for rendering, never invented by `varde-docs`.

Open the first lesson file for the user once the site is built, if you can run a CLI command to do so.

## Reference Documents

While drafting lessons, opportunistically draft reference documents where a topic warrants a compressed, revisit-later companion — command cheat sheets, an architecture diagram, a syntax reference for a pattern used repeatedly. Rendered by `varde-docs` to `./site/reference/*.html`, sharing the lesson shell/typography but excluded from the sidebar — reachable only via an inline link from the lesson that cites it. Not every module needs one.

## `NOTES.md`

Track anything the learner tells you about their background or preferences here, and record the commit SHA this snapshot was generated from. This file exists for your own reference during the conversation, not as a mechanism anything else consumes — there's no drift detection or delta generation in this skill; the update mechanism is simply running it again.
