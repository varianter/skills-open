---
name: varde-docs
description: Turn markdown or documentation content into a clean, static HTML documentation site styled with Variant's Varde design system. Use this when the user wants a README, spec, onboarding doc, or similar content turned into a ready-to-serve static documentation site "in the Varde/Variant style" — as opposed to the more general varde skill, which builds any static page/site with Varde and isn't specific to documentation.
---

# Varde Docs

Generate a static HTML documentation site from markdown/docs content, styled with Varde. This skill is the documentation-specific layer on top of the general **`varde`** skill — read `../varde/SKILL.md` and its reference material first for the actual class vocabulary, component markup, and page-shell templates. This file only covers what's specific to documentation-site generation: converting markdown by hand, deciding single vs multi-page, and generating navigation.

Like `varde`, the output must be servable as plain static files: no build step, no npm install, no JS framework. The only network dependency at page-load time is Varde's CDN stylesheet.

## When to use

The user provides markdown/text content (pasted, or pointing at existing files like a README) and wants it turned into a static documentation site. For anything that isn't documentation-shaped — a landing page, a form, general "build me a page with Varde" requests — use the `varde` skill directly instead.

## Reference material

- [../varde/SKILL.md](../varde/SKILL.md) and its `references/` — utility classes, components, page-shell templates (single-page and multi-page-with-nav), color-mode usage, and pitfalls to avoid. Required reading before generating anything.
- [references/doc-site-structure.md](./references/doc-site-structure.md) — documentation-specific guidance: single vs multi-page decision, hand-converting markdown to HTML, generating navigation, output location/naming.

## Steps

1. **Gather the content.** Read whatever markdown/files the user pointed at, or use pasted content directly. If the output location wasn't specified, ask, or default to `./docs-site/` per `doc-site-structure.md`.

2. **Decide single-page vs multi-page** per `doc-site-structure.md`'s heuristic, then build from the corresponding template in the `varde` skill's `references/page-shell.md`.

3. **Convert markdown to HTML by hand** following `doc-site-structure.md`'s conversion table. Wrap each page's content in `<div class="typeset">` — this alone gives correct heading sizes, vertical rhythm, list markers, table styling, link styling, and inline-code styling with no extra classes needed inside it.

4. **Code blocks stay plain** — `<pre><code>…</code></pre>`, no syntax highlighting (see `doc-site-structure.md` for why).

5. **For multi-page sites**, reuse the identical header+nav shell across every page, with relative links between pages and nav generated per `doc-site-structure.md`.

6. **Stick to the `varde` skill's verified classes** and consult its `pitfalls.md` before using anything color- or variant-related that you're not sure of — several things documented in Varde's own knowledge base aren't actually shipped in the CSS.

7. **Tell the user what they got**: the site is fully static (open directly or serve with any static file server), and it depends on Varde's CDN being reachable at page-load time — by design, since the point is to never bundle the design system.
