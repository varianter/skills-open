# GLOSSARY.md Format

`GLOSSARY.md` is the canonical language for this onboarding course. All lessons and reference documents should adhere to its terminology.

## Structure

```md
# {Repo or Scope Name} Glossary

{One or two sentence description of what this glossary covers.}

## Terms

**Skill**:
A packaged set of instructions for Claude Code — frontmatter plus a body — that the model or a user can invoke to carry out a specific kind of task.
_Avoid_: Plugin capability, feature

**MCP tool**:
A function a plugin's MCP server exposes to a model, registered via `definePluginTools([...])` and callable during a session.
_Avoid_: Endpoint, API call

**Monorepo workspace**:
A single repository containing multiple installable packages, managed together by a package-manager workspace feature (e.g. pnpm workspaces) rather than published/versioned independently.
_Avoid_: Multi-package repo, mono-repo
```

## Rules

- **Terms name mechanisms and this codebase's own conventions — never the business domain.** A term belongs here because the *code* or its conventions use it (a named architectural pattern, a repo-specific concept like "skill" or "widget") — not because it's part of what the business sells or why a feature exists. Unlike a PR-scoped glossary, codebase-specific vocabulary belongs here even when it isn't an external technology, because the new hire has no other place to look it up.
- **Add a term only when it's actually used.** If a concept from the scan never surfaces in a lesson, it doesn't need a glossary entry either — this isn't a dictionary of everything found, it's the vocabulary the course actually teaches.
- **Be opinionated.** When the codebase or its ecosystem uses several words for the same thing, pick the one this codebase actually uses and list the rest as aliases to avoid.
- **Keep definitions tight.** One or two sentences. Define what the term IS, not what it does or how to use it.
- **Use the glossary's own terms inside definitions.** Once a term is in the glossary, prefer it everywhere — including inside other definitions.
- **Group under subheadings** when natural clusters emerge (e.g. `## Architecture`, `## Tooling`). A flat list is fine when terms cohere.
- **No revision across runs.** Since every invocation regenerates this file from scratch, there's no "update in place as understanding deepens" step like a persistent teaching workspace would have — get it right for this snapshot.
