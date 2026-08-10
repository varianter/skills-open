# RESOURCES.md Format

`RESOURCES.md` is the curated set of trusted sources backing this course. Knowledge for lessons should be drawn from here, not from parametric guesses. Wisdom comes from the communities listed here.

## Structure

```md
# {Repo or Scope Name} Resources

## Knowledge

- [This repo's CLAUDE.md](../../CLAUDE.md)
  The repo's own documented structure and dev commands. Use for: setup, architecture, and anything else it already states authoritatively.
- [pnpm Workspaces docs](https://pnpm.io/workspaces)
  Official documentation on the workspace mechanism this repo's package management relies on. Use for: how packages reference each other and how installs/scripts propagate.
- [React docs — official](https://react.dev/)
  Primary source for the framework this codebase's UI layer is built on. Use for: component/hook mechanics referenced in lessons.

## Wisdom (Communities)

- [pnpm GitHub Discussions](https://github.com/pnpm/pnpm/discussions)
  Active, maintainer-adjacent forum. Use for: workspace/tooling questions beyond the docs.
- Internal: #eng-onboarding Slack channel
  Use for: questions specific to this team's conventions that no external doc covers.
```

## Rules

- **The repo's own docs are a primary resource, listed first.** README, CLAUDE.md/AGENTS.md, CONTRIBUTING, and any docs/ directory are Knowledge sources in their own right — cite them the same way you'd cite official framework documentation, since for anything this codebase has already documented correctly, that document *is* the deep-dive source.
- **Sources deep-dive the mechanism, not the domain.** For a framework or pattern, look for its own official docs on how the mechanism actually works — not a generic listicle about the category it belongs to.
- **High-trust and deep-dive, not just high-trust.** Prefer primary sources, official docs, and a maintainer's own writing over shallow intros — search for one per topic before writing that topic's lesson, every time.
- **Annotate every entry.** A bare link is useless later. Add one line: what it covers and when to reach for it.
- **Group by Knowledge / Wisdom.** It's fine for a resource to appear in only one group.
- **Surface gaps explicitly.** If no good resource exists for something the course needs to teach, add a `## Gaps` section listing what's missing.
- **Prune ruthlessly.** Better five sharp sources than thirty mediocre ones.
- **No cross-run reuse.** Since every invocation regenerates the whole workspace, don't assume a prior run's `RESOURCES.md` — search fresh each time, even for topics that were probably covered before.
