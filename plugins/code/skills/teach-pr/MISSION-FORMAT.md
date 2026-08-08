# MISSION.md Format

`MISSION.md` lives at the workspace root. For a PR/branch-scoped workspace the _why_ is implicit — understand what this PR changed — so this document captures the _scope_ instead: what part of the PR these lessons cover, what success looks like, and anything that bounds the approach. Every teaching decision — what to teach next, which resources to surface, which exercises to design — should trace back to this document.

## Template

```md
# Mission: {PR or branch title}

## Focus
{1-3 sentences naming the technologies/techniques the diff uses — not the domain problem it solves. If the diff spans several unrelated areas, name the ones the user chose and note the rest as out of scope for this workspace.}

## Success looks like
- {A specific, observable thing the user will be able to do}
- {Another specific thing}
- {…}

## Constraints
- {Time, prior familiarity with the tech, learning preferences, anything that bounds the approach}
```

**Example** — a PR adding ASP.NET `AuthorizationPolicy` to an endpoint plus a Swagger operation filter to document it:

```md
# Mission: Policy-based authorization on /reports endpoint

## Focus
This PR introduces ASP.NET's `AuthorizationPolicy` for endpoint-level authorization, and a Swashbuckle `IOperationFilter` to surface the required policy in generated Swagger docs. Lessons cover both mechanisms — not the underlying business permission model the policy happens to enforce.

## Success looks like
- Can write a new `AuthorizationPolicy` and register it in `AddAuthorization`
- Can explain how the ASP.NET authorization middleware evaluates a policy against a request
- Can add a custom `IOperationFilter` and explain what hook Swashbuckle calls it from

## Constraints
- No prior ASP.NET authorization experience; comfortable with C# generally
```

## Rules

- **One mission per workspace.** The PR or branch is the unifying thread: a single PR that touches several distinct techniques still gets one mission, not one workspace per technique. Two unrelated PRs are two workspaces.
- **Draft it yourself first.** Draft this automatically from the diff and PR metadata (title, description, commits, review comments), then present it to the user to confirm or edit before creating any lessons.
- **Concrete over abstract.** "Explain the new rate-limiting middleware to a teammate" beats "understand the PR."
- **Push back on vagueness.** If the diff spans several unrelated areas and it isn't clear which one to teach, ask the user to pick before writing Focus. A bad mission is worse than no mission.
- **Revise when reality shifts.** When new commits land on the PR/branch (see `SKILL.md`'s drift detection), or the user's focus moves, update this file — don't leave a stale mission steering future sessions.
- **Keep it short.** If `MISSION.md` runs past a screen, it has stopped being a compass and started being a plan.
