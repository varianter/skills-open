# Page shell templates

## Boilerplate `<head>`

Every generated page needs this — it's what actually pulls in Varde:

```html
<!doctype html>
<html lang="en" class="fg-default bg-surface-base">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Page title</title>
  <link rel="icon" type="image/svg+xml" href="https://varde.variant.dev/static/logos/variant-favicon.svg">
  <link rel="stylesheet" href="https://varde.variant.dev/v/latest/styles.css">
</head>
<body class="fs-m">
  <!-- content -->
</body>
</html>
```

`class="fg-default bg-surface-base"` on `<html>` and `class="fs-m"` on `<body>` set the baseline text color/background/font-size — keep both, they're how Varde's own docs site does it.

Fonts resolve automatically once the stylesheet above is linked (Varde's `@font-face` uses a path relative to its own CDN origin) — never add a separate font `<link>`.

## Single-page template

For one self-contained page. Wrap long-form content in `.typeset` so headings, lists, tables, links, and inline code are all styled without extra classes:

```html
<article class="stack gap-2xl mx-auto py-xl px-xl" style="max-width: 800px;">
  <header class="stack gap-xs pt-2xl pb-2xl">
    <h1 class="fs-3xl t-bold">Page title</h1>
    <p class="fs-l fg-muted">One-line description or subtitle, if there is one.</p>
  </header>

  <div class="typeset">
    <!-- long-form content: h2/h3, p, ul/ol, table, pre>code, etc. -->
  </div>
</article>
```

## Multi-page shell (shared header + sidebar nav)

For a site with several pages. Every page shares this exact shell; only `<main>`'s content and the active nav link differ. This is Varde's own docs-site pattern: a CSS grid with named areas that's a single-column top bar on mobile (nav collapsed behind a native `popover`) and reflows to a left sidebar at `768px+`. The grid/popover CSS itself isn't a Varde utility — it's page-shell CSS you write once per site, same as Varde's own docs do.

```html
<!doctype html>
<html lang="en" class="fg-default bg-surface-base">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Section title — Site title</title>
  <link rel="icon" type="image/svg+xml" href="https://varde.variant.dev/static/logos/variant-favicon.svg">
  <link rel="stylesheet" href="https://varde.variant.dev/v/latest/styles.css">
  <style>
    body {
      display: grid;
      min-height: 100svh;
      grid-template-columns: 1fr max-content;
      grid-auto-rows: max-content;
      grid-template-areas: "header nav" "main main";
    }
    .site-header { grid-area: header; }
    .site-nav { grid-area: nav; border-bottom: 1px solid var(--border-faint); }
    .site-main { grid-area: main; }

    #nav-popover {
      inset: auto;
      top: anchor(bottom);
      left: 0;
      margin: var(--spacing-l);
      width: calc(100% - (var(--spacing-l) * 2));
      border: none;
      max-height: calc(100dvh - 4rem);
      overflow-y: auto;
      background: var(--surface-dyed);
    }

    @media (min-width: 768px) {
      body {
        grid-template-columns: 240px 1fr;
        grid-template-rows: max-content auto;
        grid-template-areas: "header header" "nav main";
      }
      .site-nav { border-bottom: none; border-right: 1px solid var(--border-faint); }
      .menu-toggle { display: none; }
      #nav-popover { display: contents; }
    }
  </style>
</head>
<body class="fs-m">
  <header class="site-header stack-horizontal items-center gap-xs b-b b-faint px-s py-xs">
    <a href="index.html" class="t-bold stack-horizontal items-center gap-xs">
      <span class="fg-default lh-tight">Site title</span>
    </a>
  </header>

  <nav class="site-nav">
    <div class="stack justify-center">
      <button type="button" data-size="small" data-color="inherit" popovertarget="nav-popover"
        class="button menu-toggle mx-s my-xs" aria-label="Toggle navigation menu">
        <span aria-hidden="true">☰</span> Menu
      </button>
    </div>
    <div id="nav-popover" popover="auto">
      <div class="px-s py-m stack gap-m">
        <!-- one <div> per nav group, generated from your page/section titles -->
        <div>
          <h5 class="fs-xs fg-muted t-bold mb-2xs">Section</h5>
          <ul class="stack gap-3xs">
            <li><a class="fg-default" href="page-one.html">Page one</a></li>
            <li><a class="fg-default" href="page-two.html">Page two</a></li>
          </ul>
        </div>
      </div>
    </div>
  </nav>

  <main class="site-main">
    <article class="stack gap-2xl mx-auto py-xl px-xl" style="max-width: 1000px;">
      <header class="stack gap-xs pt-2xl pb-2xl">
        <h1 class="fs-3xl t-bold">Page title</h1>
      </header>
      <div class="typeset">
        <!-- long-form content for this page -->
      </div>
    </article>
  </main>
</body>
</html>
```

Notes:
- The `popover`/`popovertarget` attributes and CSS anchor positioning (`top: anchor(bottom)`) need no JavaScript — this is native HTML/CSS, supported in current Chromium/Safari/Firefox.
- Keep every page's `<style>` block identical across the site; only nav content, title, and `<main>` differ.
- Use relative links between generated pages (`page-two.html`, not `/page-two.html`) so the site works from any static host or straight off disk.

## Color modes (intent/status coloring)

Varde has no shipped `bg-danger-*`/`fg-success-*` utility classes. Intent coloring is done by scoping a subtree with `<color-mode palette="…">`, which repoints the neutral tokens (`surface-*`, `fg-*`, `border-*`) inside it to that palette:

```html
<color-mode palette="coral">
  <p class="fs-xs t-medium">Please enter a valid email address</p>
</color-mode>
```

Available palettes: `grey` (default), `coral`, `blue`, `yellow`, `green`, `orange`, `purple`, `teal`, `periwinkle`. Use `coral` for error/danger-style messaging (that's Varde's own convention), `green` for success, and pick sensibly among the rest for anything else — there's no fixed intent → palette mapping beyond that.

`data-color="inherit"` on `.button` (and similarly on form controls where supported) pulls its color from the nearest ancestor `<color-mode>`, letting a section use a non-default palette without any extra classes.
