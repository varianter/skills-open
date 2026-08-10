---
name: teach-pr
description: Teach the user about techniques and technology used in a pull request or branch.
disable-model-invocation: true
argument-hint: "PR number, PR URL, or branch name (optional — defaults to the current branch)"
---

The user has asked you to teach them about a specific pull request or branch. This is a stateful request - they intend to learn its contents over multiple sessions, grounded in what that PR actually changed rather than a generic topic interview.

## Identifying the PR or Branch

Work out what to diff before anything else:

- A PR number or URL: use `gh pr diff <n>` for the code diff, and `gh pr view <n> --json title,body,comments` (or equivalent) for the title, description, and review comments.
- A branch name, or nothing given: diff the current (or named) branch against the repository's default branch, e.g. `git diff origin/<default-branch>...<branch>`, plus `git log <default-branch>..<branch>` for commit messages.
- No `gh`, not authenticated, or the repo isn't hosted on a platform it supports: fall back to the plain git diff and commit messages. Skip PR title/description/review comments rather than blocking — they're enrichment, not a requirement.

Before analyzing, filter out noise that would swamp topic extraction: lockfiles, generated or vendored paths, and pure rename/format-only hunks. If what's left still spans several unrelated changes, summarize the file/topic breakdown for the user and ask them to pick a focus — don't guess which one matters.

## What Counts As A Topic

A topic is a technology, technique, API, library, or pattern the diff *uses* — never the business domain or feature it serves. The question is always "how does this code do what it does," not "what is this code for."

For example, a PR that adds ASP.NET's `AuthorizationPolicy` for endpoint authorization, and a Swagger/Swashbuckle operation filter to surface those policies in the generated API docs, has two topics: `AuthorizationPolicy` and Swagger operation-filter metadata. It does not have a topic called "permissions" or "access control" — that's the domain the code happens to serve, and teaching it would explain the wrong thing. A lesson on `AuthorizationPolicy` should deep-dive into how the ASP.NET authorization pipeline actually evaluates policies, not into the company's permission model.

This is the filter for everything downstream: `MISSION.md`'s Focus, `GLOSSARY.md`'s terms, `RESOURCES.md`'s sources, and every lesson should all be scoped to the mechanisms the diff exercises, not the problem it solves. This skill teaches **only** technology and technique — it must never produce a lesson about a business decision, domain rule, or product rationale, no matter how central that decision is to the PR.

### Categories that count as a topic

If the diff uses one of these, it's fair game — regardless of the business problem it happens to serve:

1. **Language/runtime features** — a language construct or runtime mechanism the diff exercises (source generators, discriminated unions, context managers, a new language-version feature).
2. **Libraries, frameworks, and their APIs** — a specific class/module/API/hook the diff calls into (`AuthorizationPolicy`, React's `useTransition`, `IOperationFilter`, a new package and the interface it exposes).
3. **Architectural patterns** — a structural pattern introduced or extended (CQRS, event sourcing, ports-and-adapters, a new module/layering boundary).
4. **Design patterns** — an implementation pattern in the code (strategy, decorator, builder, DI wiring, a new middleware/pipeline stage).
5. **Testing practices** — a testing technique or tool the diff adds (snapshot testing, property-based testing, a new test-double approach, contract tests).
6. **Build/tooling practices** — how code is built, linted, bundled, or generated (a bundler config change, a codegen step, a new lint rule and its rationale).
7. **Infra/deployment patterns** — how the change is deployed or operated (a Terraform module, a Kubernetes manifest change, canary rollout mechanics, a CI pipeline stage).
8. **Protocols/data formats** — a wire protocol or serialization/schema mechanism (gRPC, a GraphQL resolver, JSON Schema validation, an event schema/versioning scheme).
9. **Security mechanisms as technique** — the mechanics of a control (how OAuth2 PKCE works, how JWT signatures are validated, how a CSRF token is generated/checked) — the mechanism, never the policy it enforces.
10. **Concurrency/performance techniques** — a caching layer's invalidation strategy, an async/parallelism primitive, a rate-limiting algorithm's implementation.

This list is illustrative, not exhaustive — anything that answers "how does this code do what it does" counts even if it doesn't fit a category above.

### What does not count

- **The business domain/feature the code serves** — "access control," "billing," "inventory management." Describes what the software is *for*, not how it works.
- **Business rules encoded in the change** — "orders over $500 need approval," "trial users get 3 exports." Domain policy, not mechanism, even when it lives in the diff.
- **Product/UX rationale** — why a feature exists, what problem it solves. Belongs only in Mission's framing prose, never in a lesson.
- **Domain vocabulary/entity names** invented for this business — unless the diff also introduces a new *code mechanism* to represent them, in which case the mechanism, not the vocabulary, is the topic.
- **Configuration values with no mechanism behind them** — a changed flag default, a tuned timeout, changed copy — unless the diff also changes the mechanism reading them.

### The two-question test

Before adding anything to Focus, the Glossary, Resources, or a lesson plan, ask both:

1. "How does this code do what it does?" — names something from the checklist above → it's a topic.
2. "What is this code for?" — names a business capability → it is **not** a topic, even if it feels important. Mention it, if at all, only in Mission's framing sentence.

## Teaching Workspace

Treat `./.teach-pr/<pr-number-or-branch>/`, resolved against the root of the repository the user is working in, as the teaching workspace for this PR. See [Resolving Paths](#resolving-paths) below — this is the one place this skill deviates from "current directory."

The state of their learning is captured in this directory in several files:

- `MISSION.md`: A document capturing _what this PR changes and why it's worth learning_. Auto-drafted from the diff and PR metadata (see [The Mission](#the-mission)), then confirmed with the user. Use the format in [MISSION-FORMAT.md](./MISSION-FORMAT.md).
- `GLOSSARY.md`: The canonical terms for this PR's subject matter, adhered to in every lesson and learning record. Use the format in [GLOSSARY-FORMAT.md](./GLOSSARY-FORMAT.md).
- `RESOURCES.md`: A list of resources which can be explored to ground your teaching in contextual knowledge, or to acquire knowledge and wisdom. Use the format in [RESOURCES-FORMAT.md](./RESOURCES-FORMAT.md).
- `./learning-records/*.md`: A directory of learning records, which capture what the user has learned. These are loosely equivalent to architectural decision records in software development - they capture non-obvious lessons and key insights that may need to be revised later, or drive future sessions. These should be used to calculate the zone of proximal development. They are titled `0001-<dash-case-name>.md`, where the number increments each time. Use the format in [LEARNING-RECORD-FORMAT.md](./LEARNING-RECORD-FORMAT.md).
- `NOTES.md`: A scratchpad for you to jot down user preferences, working notes, and the last-diffed commit range (see [Detecting Drift](#detecting-drift)).
- `./site/`: The rendered documentation site for this workspace — a Varde-styled, multi-page site generated entirely by the `varde-docs` skill (see [Building the Site](#building-the-site)). **Never hand-write HTML into this directory** — it is regenerated in full each time. It contains:
  - `index.html`: the rendered Mission page (the site's entry point).
  - `glossary.html`: the rendered Glossary page.
  - `./lessons/*.html`: one file per lesson, titled `0001-<dash-case-name>.html` where the number increments each time. A **lesson** is the primary unit of teaching in this workspace — one tightly-scoped thing tied to the mission.
  - `./reference/*.html`: compressed reference materials — cheat sheets, reference algorithms, syntax — designed for quick reference, sharing the same shell/typography as lessons but not listed in the sidebar (see [Reference Documents](#reference-documents)).
  - `./site/assets/*`: the one part of `./site/` `varde-docs` must never touch — hand-authored interactive behavior only. See [Assets](#assets).

Tell the user once, the first time you create this directory, that they should add `.teach-pr/` to their `.gitignore`. Don't edit the file yourself — it's their repo, not the workspace's.

## Resolving Paths

This skill ships alongside files it references with a bare `./` — `./MISSION-FORMAT.md`, `./GLOSSARY-FORMAT.md`, and its siblings live next to `SKILL.md` in the installed skill, and should resolve there.

`./.teach-pr/` and everything under it means the opposite: it must resolve against the root of the repository the user invoked this skill in, never against this skill's own install directory. If there's any ambiguity about which repository that is, confirm it with the user before writing anything.

## The Mission

Draft `MISSION.md` yourself from the diff and PR metadata — what the PR changes, and which part of it is the focus of these lessons — then present it to the user to confirm or edit before creating any lessons. This replaces the cold "why do you want to learn this" interview: for a PR, the _why_ is implicit, so the mission captures scope (Focus, Success looks like, Constraints) instead. Use the format in [MISSION-FORMAT.md](./MISSION-FORMAT.md).

If the PR touches several distinct techniques, that's still one mission for one workspace — the PR itself is the unifying thread, not a reason to split into per-technique workspaces. If the diff is broad enough that a focus had to be chosen (see [Identifying the PR or Branch](#identifying-the-pr-or-branch)), that choice is what `Focus` records.

Missions may change as the user develops more skills and knowledge, or as the PR itself is updated (see [Detecting Drift](#detecting-drift)). Confirm with the user before changing the mission, and add a learning record to capture the change.

## Detecting Drift

Record the commit SHA (or commit range) you diffed against in `NOTES.md`. At the start of a later session, re-check it against the PR/branch's current state. If there are new commits since that SHA, tell the user and offer to extend the mission and add lessons for the new delta — don't silently keep teaching from a diff that's now stale. Generate every lesson the delta needs in one pass, the same way as [initial lesson generation](#lessons), and check the delta's topics against [prior lessons](#reusing-prior-lessons) before writing anything new.

After the delta's lessons are drafted, [build the site](#building-the-site) again — but supply the **complete** lesson set (every previously-generated lesson, unchanged, plus the new delta lessons), never just the delta. `varde-docs` has no incremental-update mode and keeps an identical sidebar across every page, so adding even one lesson means every existing page's nav must be rewritten too.

## Philosophy

To learn at a deep level, the user needs three things:

- **Knowledge**, captured from high-quality, high-trust resources — including the PR's own diff, commits, and review comments as primary sources
- **Skills**, acquired through highly-relevant interactive lessons devised by you, based on the knowledge
- **Wisdom**, which comes from interacting with other learners and practitioners

For every topic you're about to teach, actively search for a deep-dive resource on that specific mechanism from a credited, high-quality source — official documentation, a maintainer's or framework author's own writing, or a well-known blog with a strong reputation in the field. Do this before writing the lesson, every time, not just while `RESOURCES.md` is thin. Add what you find to `RESOURCES.md`. Never trust your parametric knowledge alone, and never settle for a shallow intro-level article when a deep-dive exists.

Some topics may require more skills than knowledge. A PR introducing a new algorithm might be more knowledge-based. A PR restructuring a build pipeline might be more skills-based.

### Fluency vs Storage Strength

You should be careful to split between two types of learning:

- **Fluency strength**: in-the-moment retrieval of knowledge
- **Storage strength**: long-term retention of knowledge

Fluency can give the user an illusory sense of mastery, but storage strength is the real goal. Try to design lessons which build long-term retention by desirable difficulty:

- Using retrieval practice (recall from memory)
- Spacing (distributing practice over time)
- Interleaving (mixing up different but related topics in practice - for skills practice only)

## Lessons

A lesson is the main thing you produce — the unit in which knowledge and skills reach the user. Each lesson is one tightly-scoped piece of content, tied to a numbered slot (`0001-<dash-case-name>`, incrementing each time) that becomes `./site/lessons/0001-<dash-case-name>.html` once [built](#building-the-site). Draft the lesson's content yourself — explanation, citations, quiz, exercise, and the specific links it needs out to other lessons, the glossary, and reference docs — but never hand-author the HTML: that's `varde-docs`'s job, and only after the **full** batch's content is drafted.

Once the mission is confirmed, plan and draft the **full set** of lessons its Focus needs in a single pass — see [Zone Of Proximal Development](#zone-of-proximal-development) for how to plan and sequence that set. Don't draft one lesson and stop to wait for the user to ask for the next one; the user should come back to a finished, ordered, fully linked course.

A lesson should be **beautiful** — clean, readable typography and layout — since the user will return to these later to review. Think Tufte. This is `varde-docs`'s responsibility to deliver, using Varde's design system — see [Building the Site](#building-the-site).

The lesson should be short, and completable very quickly. Learners' working memory is very small, and we need to stay within it. But each lesson should give the user a single tangible win that they can build on. It should be directly tied to the mission, and should be in the user's zone of proximal development.

If possible, open the first lesson file (`./site/lessons/0001-<dash-case-name>.html`) for the user by running a CLI command once the site is built.

Each lesson needs real links to other lessons, `index.html` (Mission), and `glossary.html` (with anchors to specific terms where useful) — never to the `.md` sources. Specify these links as part of the lesson content you hand to `varde-docs` (see [Building the Site](#building-the-site)) — they're your decision, computed during [sequencing](#zone-of-proximal-development), not something `varde-docs` invents.

Each lesson's practice step should send the user back to the real thing to read themselves — the actual diff hunk, file, or commit this PR touched — rather than a paraphrase of the code. The lesson provides scaffolding (what to look for, the glossary terms, the question to answer) and a recommended primary source beyond the PR itself; it should never substitute for reading the PR. The goal is a foundation for understanding this PR's contents without leaning on the assistant, not a summary to consume instead of the code.

Each lesson should contain a reminder to ask followup questions to the agent. The agent is their teacher, and can assist with anything that's unclear.

## Building the Site

`MISSION.md`, `GLOSSARY.md`, and each lesson's drafted content are editable sources of truth — but the user reads a polished, linked site, not raw markdown. That site is always built by invoking the sibling **`varde-docs`** skill; never hand-author any HTML yourself.

Invoke it once per full batch — after the mission is confirmed, the glossary is drafted, and every lesson in the ordered [ZPD](#zone-of-proximal-development) batch has its content drafted. Never invoke it per-lesson or mid-draft. On drift, invoke it again with the complete accumulated lesson set — see [Detecting Drift](#detecting-drift).

Since `varde-docs` takes free-form instructions rather than a fixed schema, the request you hand it must spell out, explicitly:

- **Output path**: `.teach-pr/<pr-number-or-branch>/site/` (resolved per [Resolving Paths](#resolving-paths)), overwriting `index.html`, `glossary.html`, and everything under `lessons/` and `reference/` — but never touching `./site/assets/` (see [Assets](#assets)).
- **Exact filenames, given verbatim** — `index.html` for the rendered Mission, `glossary.html` for the rendered Glossary, `lessons/0001-<dash-case-name>.html` etc. for each lesson in teaching order. Don't let `varde-docs` invent kebab-case names from titles; the numeric prefixes encode teaching order and are referenced by learning records and other workspaces, so they must be preserved byte-for-byte.
- **A sidebar with exactly two sections, in this order**:
  1. **"Lessons"** — one item per lesson, in teaching order, link text = lesson title, linking to `lessons/000N-<dash-case-name>.html`.
  2. **"Glossary / Mission"** — exactly two items: "Mission" → `index.html`, "Glossary" → `glossary.html`.
  - No third section. Reference pages exist under `reference/` but are deliberately excluded from the sidebar — link to them only inline, from the lesson content that cites them.
- **The content itself** — the Mission, the Glossary, and every lesson's drafted content (including the specific links each lesson needs out to other lessons, glossary terms, and reference pages) — passed through for rendering, not invented by `varde-docs`.

This makes "generate all lessons and have them linked together" an enforceable data flow: you compute the full lesson set and its required links during [sequencing](#zone-of-proximal-development); `varde-docs` is only responsible for rendering them as real, working links inside one shared shell.

## Reusing Prior Lessons

Before planning which lessons to generate, check whether a topic has already been taught — not only in this workspace, but in any sibling workspace under `.teach-pr/` (other PRs or branches the user has learned from before). Scan other workspaces' `GLOSSARY.md` terms and `./site/lessons/*.html` titles for overlap with what this mission's Focus requires.

- If a topic was already covered elsewhere, don't regenerate a lesson for it — link to the existing lesson instead (a relative link across workspaces, e.g. `../../<other-pr-or-branch>/site/lessons/0003-foo.html`), and treat it as already satisfied when sequencing the new batch in [Zone Of Proximal Development](#zone-of-proximal-development).
- If the existing lesson only partially covers what this mission needs, write a short new lesson for the gap that links back to the original for the shared foundation, rather than re-explaining it from scratch.
- Still add the term to this workspace's `GLOSSARY.md` — it's canonical for this PR too — but note next to it that it's covered in depth elsewhere, with a link.
- Note the reuse in `NOTES.md` so a future session understands why a topic has no numbered lesson of its own here.
- This follows directly from [What Counts As A Topic](#what-counts-as-a-topic): topics are mechanisms, so the same mechanism reappearing in a different PR is genuinely the same topic, not a new one that needs re-teaching.

## Assets

Visual design is no longer this skill's responsibility — `varde-docs` renders every page from Varde's own stylesheet, so lessons look like one consistent course without a hand-built stylesheet. `./site/assets/` exists only for **interactive behavior** that HTML alone can't provide, and is the one part of `./site/` that [building the site](#building-the-site) must never overwrite.

Prefer native, zero-JS HTML first: a `<details><summary>` reveal-answer block, or Varde's `.checkbox`/radio inputs paired with a reveal, covers most quiz interactions without any script at all. Only write a `<script>` when a genuine interactive simulator can't be expressed natively — scope it to that one lesson if it's a one-off, or to a shared file in `./site/assets/` (e.g. `quiz.js`) if reused across 2+ lessons. Before authoring anything new, read `./site/assets/` and reuse what's already there rather than duplicating it.

## Zone Of Proximal Development

Each lesson, the user should always feel as if they are being challenged 'just enough'. This applies across the whole batch, not lesson-by-lesson: plan the entire set the mission's Focus needs before generating any of it, and sequence that set from foundational to advanced so each lesson's prerequisites are covered by the ones before it.

Plan that sequence by:

- Reading their `learning-records` to establish what's already known — don't re-teach it, and don't let it break the [reuse check](#reusing-prior-lessons) either
- Breaking the mission's Focus into the full list of distinct things that need teaching, based on what the diff actually exercises — each candidate must pass the [two-question test](#the-two-question-test) before being added to the list
- Checking that list against [prior lessons](#reusing-prior-lessons) elsewhere under `.teach-pr/`, dropping or shrinking anything already covered
- Ordering what's left by dependency and difficulty, not diff order
- Generating a lesson for every item in the ordered list before stopping

If the user specifies an exact thing they want to learn about the PR, apply the same process scoped to that thing — it may still expand into more than one lesson, and all of them should be generated together.

## Knowledge

Lessons should be designed around a skill the user is going to learn. The knowledge in the lesson should be only what's required to acquire that skill. You teach the knowledge first, then get the user to practice the skills via an interactive feedback loop.

Knowledge should first be gathered from trusted resources: before writing a lesson, search for a deep-dive resource on its topic from a credited, high-quality source (see [Philosophy](#philosophy)) and record it in `RESOURCES.md`. Prefer sources that go deep on the specific mechanism — official docs, architecture guides, a maintainer's own writing — over shallow tutorials or listicles. Lessons should be littered with citations - links to external resources, and to the specific files/commits/comments in the PR, to back up any claim made. This increases the trustworthiness of the lesson.

For acquiring knowledge, difficulty is the enemy. It eats working memory you need for understanding.

## Skills

If knowledge is all about acquisition, skills are about durability and flexibility. Make the knowledge stick.

For skill acquisition, difficulty is the tool. Effortful retrieval is what builds storage strength. Skills should be taught through interactive lessons. There are several tools at your disposal:

- Interactive lessons, using quizzes and light in-browser tasks
- Lessons which guide the user through reading specific parts of the PR themselves and reporting back what they found

Each of these should be based on a **feedback loop**, where the user receives feedback on their performance. This feedback loop should be as tight as possible, giving feedback immediately - and ideally automatically.

For quizzes, each answer should be exactly the same number of words (and characters, if possible). Don't give the user any clues about the answer through formatting.

## Acquiring Wisdom

Wisdom comes from true real-world interaction - testing your skills outside the learning environment.

When the user asks a question that appears to require wisdom, your default posture should be to attempt to answer - but to ultimately delegate to a **community**.

A community is a place (online or offline) where the user can test their skills in the real world. This might be a forum, a subreddit, an internal team channel, or a local interest group.

You should attempt to find high-reputation communities the user can join. If the user expresses a preference that they don't want to join a community, respect it.

## Reference Documents

While drafting lessons, you should also draft reference documents, rendered by `varde-docs` to `./site/reference/*.html` alongside the lessons (see [Building the Site](#building-the-site)). Lessons can reference these documents - they are useful for tracking raw units of knowledge useful across lessons. They share the same shell and typography as lessons, but are deliberately left out of the sidebar — reach them only via an inline link from the lesson that cites them.

Lessons will rarely be revisited later - reference documents will be. They should be the compressed essence of the lesson, in a format designed for quick reference.

Some things lend themselves to reference:

- Syntax and code snippets for the language or framework involved
- Algorithms and flowcharts for processes the PR introduces
- Architecture or sequence diagrams for how the change fits together
- Glossaries for any nomenclature specific to the PR's domain

`GLOSSARY.md` in particular is an essential reference. Once it's created, it should be adhered to in every lesson.

## `NOTES.md`

The user will sometimes express preferences of how they want to be taught, or things you should keep in mind. This is also where you track the last-diffed commit range for [drift detection](#detecting-drift).
