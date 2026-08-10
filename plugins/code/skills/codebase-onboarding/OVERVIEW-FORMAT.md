# OVERVIEW.md Format

`OVERVIEW.md` lives at the workspace root. It captures what this codebase (or scoped path) is, which modules this course covers, and what success looks like — the compass for every lesson generated from it. Confirm it with the learner before drafting any lesson content.

## Template

```md
# Onboarding Overview: {Repo or Scope Name}

## Snapshot
{One or two sentences: what this codebase (or scoped path) is and does. Generated at commit {SHA} on {date}. Scope: {whole repo | specific package/path}.}

## Modules
- **{Module name}** — {one sentence on what it covers}
- **{Module name}** — {one sentence on what it covers}
- {…one line per module, including any discovered beyond the mandatory five}

## Success looks like
- {A specific, observable thing the learner will be able to do}
- {Another specific thing}
- {…}

## Constraints
- {The learner's stated prior experience, preferences, or role — generic if the learner isn't the one confirming this document}
```

**Example** — onboarding to a pnpm-workspace monorepo of Claude Code plugins:

```md
# Onboarding Overview: variant-skills (plugins/code)

## Snapshot
A pnpm-workspace monorepo of Claude Code plugins, each with skills, optional MCP tools, and a deploy workflow. Generated at commit a1b2c3d on 2026-08-10. Scope: plugins/code.

## Modules
- **Setup & Local Development** — pnpm workspace install, dev/build scripts, and the plugin-per-directory layout.
- **Architecture** — how a plugin's skills, MCP tools, and manifest fit together.
- **Testing** — how skills are validated with the repo's own CLI tooling.
- **Patterns** — how MCP tools structure request context and error handling.
- **Frameworks & Libraries** — @variant/mcp-server, and the Vite-based widget build.
- **Skill Authoring Conventions** — the SKILL.md/README/references convention shared across skills (discovered).

## Success looks like
- Can run a plugin's MCP server locally and hot-reload a widget
- Can explain how a new skill registers an MCP tool without a widget
- Can validate a skill with the repo's own tooling before opening a PR

## Constraints
- Comfortable with TypeScript and Node tooling; new to Claude Code plugin/skill conventions specifically
```

## Rules

- **Draft it yourself first.** Draft this automatically from the repo scan — documented setup, directory structure, manifests, discovered patterns — then present it to the learner to confirm or edit before creating any lessons.
- **One overview per course.** A monorepo scoped to one package still gets one overview for that scope; scanning the whole repo gets one overview for the whole repo. Don't split a single run into multiple overviews.
- **Concrete over abstract.** "Can run the test suite and explain what it covers" beats "understand the codebase."
- **Push back on vagueness.** If the scan turns up a scope so broad that a focus had to be chosen (e.g. a huge monorepo with unrelated services), say so and ask the learner to scope down rather than guessing.
- **Keep it short.** If `OVERVIEW.md` runs past a screen, it has stopped being a compass and started being documentation — trim the module list, not the lessons.
- **No drift tracking here.** Unlike a PR-scoped mission, this document isn't revised in place across sessions — the next run regenerates a new one from scratch.
