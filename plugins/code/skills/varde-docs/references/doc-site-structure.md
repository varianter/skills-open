# Doc-site structure

This is the documentation-specific layer on top of the `varde` skill's general page-shell templates ([../../varde/references/page-shell.md](../../varde/references/page-shell.md)). It covers what's specific to turning markdown/docs content into a site: converting markdown by hand, deciding single vs multi-page, and generating navigation.

## Deciding single-page vs multi-page

- One file, or content that reads as one continuous topic → a single self-contained page using the `varde` skill's single-page template.
- Several distinct files, or one long file that reads like several distinct chapters/sections → a multi-page site sharing one header+sidebar-nav shell (the `varde` skill's multi-page template), one HTML file per section/file.
- Default to what the input naturally implies — don't ask the user unless it's genuinely ambiguous (e.g. a single very long file where it's unclear whether the user wants it split).

## Converting markdown to HTML by hand

No markdown-to-HTML library or client-side parser — the output must work with zero build step. Translate directly:

| Markdown | HTML |
| --- | --- |
| `# H1` / `## H2` / etc. | `<h1>`…`<h4>` (inside `.typeset`, sizes/spacing are automatic) |
| paragraph | `<p>` |
| `- item` / `1. item` | `<ul>`/`<ol>` with `<li>` |
| `` `code` `` | `<code>` (inline, auto-styled inside `.typeset`) |
| fenced code block | `<pre><code>…</code></pre>` — **plain, no syntax highlighting**. Varde's own syntax highlighting is a build-time pipeline (remark/rehype/shiki); replicating it would break the no-build-step requirement here. |
| `[text](url)` | `<a href="…">` (auto-styled inside `.typeset`) |
| table | `<table>` with `<thead>`/`<tbody>` (auto-styled inside `.typeset`, no `.table` class needed) |
| `> quote` | `<blockquote>` |

Keep one `<h1>` per page (the page title, in the page header outside `.typeset` — see the single-page template) and start in-content headings at `<h2>`, matching Varde's own docs convention of a page-level `<h1>` + `<h2>`-anchored sections.

## Generating navigation for multi-page sites

- One nav item per generated page, grouped under a `<h5>` section label if the source content has a natural higher-level grouping (e.g. multiple markdown files that live in the same subfolder, or one file's top-level headings if you split a single long file into pages).
- Use the page's title (its `<h1>`/first heading) as the nav link text.
- Nav links must be relative (`page-two.html`, not `/page-two.html`) — the site needs to work from any static host or straight off disk.
- Keep the nav identical across every page in the site; only the active/current page and `<main>`'s content differ.

## Output

- Default output location: `./docs-site/` in the current working directory, unless the user specifies otherwise.
- File naming: kebab-case from the source file/section title (e.g. `getting-started.html`), with an `index.html` as the entry point for multi-page sites.
- After generating, tell the user the site is static (open directly or serve with any static file server) and that it needs Varde's CDN reachable at page-load time.
