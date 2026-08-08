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

This is the filter for everything downstream: `MISSION.md`'s Focus, `GLOSSARY.md`'s terms, `RESOURCES.md`'s sources, and every lesson should all be scoped to the mechanisms the diff exercises, not the problem it solves.

## Teaching Workspace

Treat `./.teach-pr/<pr-number-or-branch>/`, resolved against the root of the repository the user is working in, as the teaching workspace for this PR. See [Resolving Paths](#resolving-paths) below — this is the one place this skill deviates from "current directory."

The state of their learning is captured in this directory in several files:

- `MISSION.md`: A document capturing _what this PR changes and why it's worth learning_. Auto-drafted from the diff and PR metadata (see [The Mission](#the-mission)), then confirmed with the user. Use the format in [MISSION-FORMAT.md](./MISSION-FORMAT.md).
- `GLOSSARY.md`: The canonical terms for this PR's subject matter, adhered to in every lesson and learning record. Use the format in [GLOSSARY-FORMAT.md](./GLOSSARY-FORMAT.md).
- `./reference/*.html`: A directory of reference materials. These are the compressed learnings from the lessons - cheat sheets, reference algorithms, syntax. They are the raw units of learning. They should be beautiful documents which print out well, and are designed for quick reference.
- `RESOURCES.md`: A list of resources which can be explored to ground your teaching in contextual knowledge, or to acquire knowledge and wisdom. Use the format in [RESOURCES-FORMAT.md](./RESOURCES-FORMAT.md).
- `./learning-records/*.md`: A directory of learning records, which capture what the user has learned. These are loosely equivalent to architectural decision records in software development - they capture non-obvious lessons and key insights that may need to be revised later, or drive future sessions. These should be used to calculate the zone of proximal development. They are titled `0001-<dash-case-name>.md`, where the number increments each time. Use the format in [LEARNING-RECORD-FORMAT.md](./LEARNING-RECORD-FORMAT.md).
- `./lessons/*.html`: A directory of lessons. A **lesson** is a single, self-contained HTML output that teaches one tightly-scoped thing tied to the mission. This is the primary unit of teaching in this workspace.
- `./assets/*`: Reusable **components** shared across lessons. See [Assets](#assets).
- `NOTES.md`: A scratchpad for you to jot down user preferences, working notes, and the last-diffed commit range (see [Detecting Drift](#detecting-drift)).

Tell the user once, the first time you create this directory, that they should add `.teach-pr/` to their `.gitignore`. Don't edit the file yourself — it's their repo, not the workspace's.

## Resolving Paths

This skill ships alongside files it references with a bare `./` — `./MISSION-FORMAT.md`, `./GLOSSARY-FORMAT.md`, and its siblings live next to `SKILL.md` in the installed skill, and should resolve there.

`./.teach-pr/` and everything under it means the opposite: it must resolve against the root of the repository the user invoked this skill in, never against this skill's own install directory. If there's any ambiguity about which repository that is, confirm it with the user before writing anything.

## The Mission

Draft `MISSION.md` yourself from the diff and PR metadata — what the PR changes, and which part of it is the focus of these lessons — then present it to the user to confirm or edit before creating any lessons. This replaces the cold "why do you want to learn this" interview: for a PR, the _why_ is implicit, so the mission captures scope (Focus, Success looks like, Constraints) instead. Use the format in [MISSION-FORMAT.md](./MISSION-FORMAT.md).

If the PR touches several distinct techniques, that's still one mission for one workspace — the PR itself is the unifying thread, not a reason to split into per-technique workspaces. If the diff is broad enough that a focus had to be chosen (see [Identifying the PR or Branch](#identifying-the-pr-or-branch)), that choice is what `Focus` records.

Missions may change as the user develops more skills and knowledge, or as the PR itself is updated (see [Detecting Drift](#detecting-drift)). Confirm with the user before changing the mission, and add a learning record to capture the change.

## Detecting Drift

Record the commit SHA (or commit range) you diffed against in `NOTES.md`. At the start of a later session, re-check it against the PR/branch's current state. If there are new commits since that SHA, tell the user and offer to extend the mission and add lessons for the new delta — don't silently keep teaching from a diff that's now stale.

## Philosophy

To learn at a deep level, the user needs three things:

- **Knowledge**, captured from high-quality, high-trust resources — including the PR's own diff, commits, and review comments as primary sources
- **Skills**, acquired through highly-relevant interactive lessons devised by you, based on the knowledge
- **Wisdom**, which comes from interacting with other learners and practitioners

Before the `RESOURCES.md` is well-populated, your focus should be to find high-quality resources which will help the user acquire knowledge. Never trust your parametric knowledge.

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

A lesson is the main thing you produce — the unit in which knowledge and skills reach the user. Each lesson is one self-contained HTML file, saved to `./lessons/` and titled `0001-<dash-case-name>.html` where the number increments each time.

A lesson should be **beautiful** — clean, readable typography and layout — since the user will return to these later to review. Think Tufte.

The lesson should be short, and completable very quickly. Learners' working memory is very small, and we need to stay within it. But each lesson should give the user a single tangible win that they can build on. It should be directly tied to the mission, and should be in the user's zone of proximal development.

If possible, open the lesson file for the user by running a CLI command.

Each lesson should link via HTML anchors to other lessons and reference documents.

Each lesson's practice step should send the user back to the real thing to read themselves — the actual diff hunk, file, or commit this PR touched — rather than a paraphrase of the code. The lesson provides scaffolding (what to look for, the glossary terms, the question to answer) and a recommended primary source beyond the PR itself; it should never substitute for reading the PR. The goal is a foundation for understanding this PR's contents without leaning on the assistant, not a summary to consume instead of the code.

Each lesson should contain a reminder to ask followup questions to the agent. The agent is their teacher, and can assist with anything that's unclear.

## Assets

Lessons are built from reusable **components**, stored in `./assets/`: stylesheets, quiz widgets, simulators, diagram helpers — anything a second lesson could reuse.

Reuse is the default, not the exception. Before authoring a lesson, read `./assets/` and build from the components already there. When a lesson needs something new and reusable, write it as a component in `./assets/` and link to it — never inline code a future lesson would duplicate.

A shared stylesheet is the first component every workspace earns: every lesson links it, so the lessons look like one consistent course rather than a pile of one-offs. As the workspace grows, so should the component library.

## Zone Of Proximal Development

Each lesson, the user should always feel as if they are being challenged 'just enough'.

The user may specify an exact thing they want to learn about the PR. If they don't, figure out their zone of proximal development by:

- Reading their `learning-records`
- Figuring out the right thing to teach them based on the mission and what remains unexplored in the diff
- Teach the most relevant thing that fits in their zone of proximal development

## Knowledge

Lessons should be designed around a skill the user is going to learn. The knowledge in the lesson should be only what's required to acquire that skill. You teach the knowledge first, then get the user to practice the skills via an interactive feedback loop.

Knowledge should first be gathered from trusted resources. Use `RESOURCES.md` to keep track of them. Lessons should be littered with citations - links to external resources, and to the specific files/commits/comments in the PR, to back up any claim made. This increases the trustworthiness of the lesson.

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

While creating lessons, you should also create reference documents. Lessons can reference these documents - they are useful for tracking raw units of knowledge useful across lessons.

Lessons will rarely be revisited later - reference documents will be. They should be the compressed essence of the lesson, in a format designed for quick reference.

Some things lend themselves to reference:

- Syntax and code snippets for the language or framework involved
- Algorithms and flowcharts for processes the PR introduces
- Architecture or sequence diagrams for how the change fits together
- Glossaries for any nomenclature specific to the PR's domain

`GLOSSARY.md` in particular is an essential reference. Once it's created, it should be adhered to in every lesson.

## `NOTES.md`

The user will sometimes express preferences of how they want to be taught, or things you should keep in mind. This is also where you track the last-diffed commit range for [drift detection](#detecting-drift).
