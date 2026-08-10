---
name: varde
description: Build a static HTML page or small site styled with Variant's Varde design system — utility classes, buttons, forms, tables, typography, and page layout. Use this any time the user wants something "in the Varde/Variant style", or wants a static page/site built on top of Varde generally (not just documentation — the varde-docs skill builds on this one specifically for turning markdown/docs into a documentation site). Links Varde's CSS from its CDN; never bundles or reimplements it.
---

# Varde

Build static HTML pages styled with Varde, Variant's design system, by linking Varde's CSS from its CDN and using its real utility-class and component vocabulary — never bundling, copying, or reimplementing Varde's CSS.

Output must be servable as plain static files: no build step, no npm install, no JS framework required for styling (a little inline `<style>` for one-off page-shell layout, and native HTML like `popover`, are fine — see `references/page-shell.md`). The only network dependency at page-load time is Varde's CDN stylesheet.

## When to use

Any request to build or style a static page/small site "like Varde" or "in the Variant design system" — landing pages, internal tools' UI, a one-off form, a marketing page, etc. If the request is specifically about turning markdown/documentation content into a documentation site, prefer the `varde-docs` skill instead — it builds on this skill and adds markdown-conversion and multi-page-nav-generation logic on top.

## Reference material

Read these before generating anything — they're the difference between output that looks right and output that's subtly wrong:

- [references/utility-classes.md](./references/utility-classes.md) — verified layout, spacing, typography, color, and border utility classes.
- [references/components.md](./references/components.md) — button, form control, table, link, icon, and spinner markup.
- [references/page-shell.md](./references/page-shell.md) — the HTML boilerplate, a single-page template, a multi-page header+sidebar-nav shell, and color-mode usage for intent/status coloring.
- [references/pitfalls.md](./references/pitfalls.md) — classes and patterns that look plausible (some are even in Varde's own knowledge-base docs) but aren't actually shipped. Check this before using anything color- or variant-related that you're not 100% sure of.

## Steps

1. **Start from the boilerplate `<head>`** in `page-shell.md`: doctype, `<html lang="…" class="fg-default bg-surface-base">`, the Varde CDN stylesheet link (`https://varde.variant.dev/v/latest/styles.css`), and the Varde favicon link. Never add a separate font `<link>` — Varde's font is baked into that stylesheet.

2. **Build markup with verified classes only.** Use `utility-classes.md` for layout/spacing/typography/color/borders and `components.md` for buttons/forms/tables/links/icons/spinners. No class prefix — classes are short and unprefixed (`stack`, `p-m`, `fg-muted`), and variants are `data-*` attributes (`data-color`, `data-variant`, `data-size`), not modifier classes.

3. **Wrap long-form/prose content in `.typeset`** — it styles headings, lists, tables, links, and inline code automatically, with correct vertical rhythm, with no extra classes needed inside it.

4. **Use `<color-mode palette="…">` for intent/status coloring**, never a made-up utility class — Varde has no shipped `bg-danger-*`/`fg-success-*` classes. See `page-shell.md` for the palette list and `pitfalls.md` for why.

5. **Check `pitfalls.md` before using anything you're not sure exists.** A few classes/variants are documented in Varde's own knowledge base but aren't actually in the shipped CSS (e.g. `fg-inverted`, button `data-variant="tinted"`, any intent-color utility class, icons beyond `plus`/`pencil`/`cage`). Using them silently produces unstyled markup — worse than an error, since it's easy to miss.

6. **If a genuinely new visual need isn't covered by a verified class**, write a small scoped `<style>` block (as the multi-page shell in `page-shell.md` does for its grid layout) rather than guessing at a utility class name that might not exist.

7. **Tell the user what they got**: the output is fully static (can be opened directly or served by any static file server), and it depends on Varde's CDN being reachable at page-load time — by design, since the point is to never bundle the design system.
