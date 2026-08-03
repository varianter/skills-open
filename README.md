# plugin-marketplace-template

> This repo is based on the [Variant Claude plugin marketplace template](https://github.com/varianter/plugin-marketplace-template).

## Use skills from this template

Use skills from plugins in this repo by connecting marketplace to Claude, or adding the skills specifically:

```
npx skills add varianter/skills-open

# list all
npx skills add varianter/skills-open --list
```

Keep in mind that if you add a skill that refers to MCP tools outside of Claude marketplace, you would have to connect to MCP server manually.

## Plugins in this repo

- **`plugins/handbook`** — Get information from Variant Handbook.

## Contributing

### Adding a skill

Create `plugins/standard/skills/<name>/SKILL.md`. If the skill needs MCP tools, add them under `plugins/standard/skills/<name>/tools/`:

```
---
name: my-skill
description: One-line description shown in the skill picker
---

Skill instructions here.
```

Validate it:

```bash
pnpm validate-skill plugins/standard/skills/my-skill
```

### Adding an MCP tool

Create `plugins/standard/tools/<name>/<toolName>.ts` with an explicit registrar:

```typescript
import type { McpServer } from '@variant/mcp-server';
import { z } from 'zod';

export function registerMyTool(server: McpServer): void {
  server.registerTool(
    'my-tool',
    { title: 'My Tool', description: 'Does something useful', inputSchema: { param: z.string() } },
    async ({ param }) => ({ content: [{ type: 'text', text: param }] }),
  );
}
```

Then add it to `plugins/standard/mcp-server/registerTools.ts`:

```typescript
import { definePluginTools } from '@variant/mcp-server';
import { registerMyTool } from '../tools/my-tool/myTool.js';

export const registerTools = definePluginTools([registerMyTool]);
```

For skill-colocated tools (tools under a skill's `tools/` directory) see [`AGENTS.md`](AGENTS.md).

Note: You can also use the `create-plugin-skill` skill in `.agents/skills/create-plugin-skill/SKILL.md` to generate a new skill directory with the correct structure.

### Adding a new plugin

1. Copy `plugins/standard/` to `plugins/<name>/`
2. Update `plugins/<name>/.claude-plugin/plugin.json` and `plugins/<name>/package.json`
3. Add the new plugin to `.claude-plugin/marketplace.json`
4. Add it to the plugin matrices in `.github/workflows/ci.yml` and `.github/workflows/deploy.yml`


Note: You can also use the `create-plugin` skill in `.agents/skills/create-plugin/SKILL.md` to generate a new plugin directory with the correct structure.

### Development commands

From the **repo root**:

```bash
pnpm dev [plugin]        # selected plugin — server + widget watcher (hot-reload)
pnpm dev:server [plugin] # selected plugin — server only (faster, skips widgets)
pnpm dev:standard        # standard plugin alias
pnpm build               # build all plugin servers
pnpm typecheck           # type-check workspace packages
pnpm check               # biome lint + format check
pnpm fix                 # biome auto-fix
pnpm validate-skills      # validate all skills
pnpm validate-skill <path> # validate a single skill, e.g. plugins/standard/skills/my-skill
```

Additional commands available from **`plugins/standard/`**:

```bash
pnpm inspect       # official MCP Inspector
```

## Deployment

Trigger the **Deploy** GitHub Actions workflow from the repository UI. Select a plugin (or "all") and environment. Each plugin has its own Docker image (`<plugin>-mcp`) built from `plugins/<plugin>/mcp-server/Dockerfile`.

Docker builds install `@variant/mcp-server` from npm, so publish the package before building images from this template.

Update the registry and deployment target in `.github/workflows/deploy.yml` to match your infrastructure.

See [`CLAUDE.md`](CLAUDE.md) for the full structure reference and [`AGENTS.md`](AGENTS.md) for the tool authoring guide.
