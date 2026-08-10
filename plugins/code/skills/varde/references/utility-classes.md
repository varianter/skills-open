# Varde utility classes (verified)

No class prefix — everything is a short, unprefixed utility name (`stack`, `p-m`, `fg-muted`), never `vgi-*`/`vd-*`. Variants are set with `data-*` attributes, not BEM modifier classes.

Everything below is confirmed against Varde's compiled CSS, not just its knowledge-base docs (some of those docs describe classes that aren't actually shipped — see [pitfalls.md](./pitfalls.md)).

## Spacing scale

Named steps, smallest to largest: `4xs 3xs 2xs xs s m l xl 2xl 3xl 4xl 5xl 6xl`. `m` is roughly `1rem` (fluid via `clamp()`, so it grows slightly on wide viewports).

```html
<!-- padding: prefix p, optional direction x/y/t/r/b/l -->
<div class="p-m px-l pt-xs"></div>

<!-- margin: prefix m, same directions. Negative margin: mt--s -->
<div class="mt-xl mx-auto ml-auto"></div>

<!-- gap, for flex/grid containers -->
<div class="gap-m">
  <div class="gap-row-s gap-column-xl"></div>
</div>
```

## Layout

Flexbox-based. There is no CSS-grid utility system beyond `.pile` — build multi-column layouts with `.stack-horizontal` + `gap-*`, or a one-off `<style>` block if you truly need a grid (see `page-shell.md` for how the Varde docs site itself does this for its page chrome).

```html
<!-- column, top-aligned -->
<div class="stack gap-m"></div>

<!-- row, vertically centered -->
<div class="stack-horizontal gap-s"></div>

<!-- row, aligned to top / bottom -->
<div class="stack-horizontal items-start gap-s"></div>
<div class="stack-horizontal items-end gap-s"></div>

<!-- row, children stretch full height -->
<div class="stack-horizontal items-stretch"></div>
```

Modifiers: `.grow` `.shrink` `.flex-1` `.justify-start/end/center/between` `.nowrap` `.inline-flex` `.self-stretch` (single-child cross-axis stretch).

`.pile` stacks all children in one grid cell (for overlays/badges) — children stay in normal flow, unlike `position: absolute`:

```html
<div class="pile">
  <img src="…" />
  <span class="badge">3</span>
</div>
```

## Typography

```html
<span class="fs-xs"></span>  <!-- through fs-s, fs-m (default body size), fs-l, fs-xl, fs-2xl, fs-3xl, fs-4xl -->

<span class="t-regular"></span>  <!-- font-weight 400 -->
<span class="t-medium"></span>   <!-- font-weight ~525 -->
<span class="t-bold"></span>     <!-- font-weight ~650 -->

<span class="t-left"></span>
<span class="t-center"></span>
<span class="t-right"></span>
<span class="t-uppercase"></span>

<span class="lh-tight"></span>
<span class="lh-normal"></span>
<span class="lh-relaxed"></span>
```

Prefer wrapping actual document/article content in `.typeset` instead of hand-styling every heading/paragraph — see `page-shell.md`.

## Color (neutral only)

Varde's color model is semantic, never raw palette values. Only **neutral** utility classes are shipped as standalone classes:

```html
<!-- Surfaces (ambient background) -->
<div class="bg-surface-base"></div>
<div class="bg-surface-tinted"></div>
<div class="bg-surface-dyed"></div>

<!-- Text -->
<p class="fg-default"></p>   <!-- body text -->
<p class="fg-muted"></p>     <!-- secondary/helper text -->
<p class="fg-emphasis"></p>  <!-- headings, labels -->

<!-- Border color -->
<div class="b-default"></div>   <!-- everyday border -->
<div class="b-faint"></div>     <!-- quieter line -->
<div class="b-prominent"></div> <!-- rare, structural emphasis -->
```

There is **no** `fg-inverted`, and **no** shipped `bg-danger-*`/`fg-success-*`/`border-warning-*` style intent-color utility classes, despite some of Varde's own knowledge docs describing them. For intent/status coloring (errors, success states, palette accents), use `<color-mode palette="…">` instead — see `page-shell.md` and `pitfalls.md`.

## Borders

```html
<div class="b-all"></div>   <!-- all sides, 1px solid -->
<div class="b-t"></div>     <!-- top only -->
<div class="b-r"></div>
<div class="b-b"></div>
<div class="b-l"></div>
<div class="b-last-none"></div>   <!-- removes border from :last-child -->
<div class="b-first-none"></div> <!-- removes border from :first-child -->
```

Always pair a border-side class with a border-color class: `<li class="b-b b-default">`.

Border radius:

```html
<div class="br-none"></div>    <!-- 0 -->
<div class="br-xs"></div>      <!-- 2px -->
<div class="br-s"></div>       <!-- 4px -->
<div class="br-m"></div>       <!-- 8px -->
<div class="br-l"></div>       <!-- 12px -->
<div class="br-xl"></div>      <!-- 16px -->
<div class="br-2xl"></div>     <!-- 24px -->
<div class="br-pill"></div>    <!-- 9999px -->
<div class="br-circle"></div>  <!-- 50% -->
<div class="br-inherit"></div> <!-- inherits parent's radius, useful when clipping -->
```

## Misc

```html
<div class="of-hidden"></div>       <!-- overflow: hidden -->
<div class="of-clip"></div>         <!-- overflow: clip -->
<div class="of-scroll"></div>       <!-- overflow: scroll -->
<div class="aspect-ratio-1-1"></div>
```

## When to use `.stack` vs margin/padding

- More than two elements in the same container → use `.stack` with a single `gap-*`.
- One element alone with no background → use `.m-*` classes.
- Uniform-looking items (lists, cards) → uniform spacing between them.
- Dissimilar elements (e.g. a label + its input + a validation message) → don't force uniform spacing; hand-tune each gap instead of relying on `.stack`'s single gap value. Nesting generally wants tighter spacing (e.g. a label-to-input gap of `3xs`) than groups of inputs (`s`/`m`).
