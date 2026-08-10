# Varde components (verified)

## Button

Apply `.button` to a `<button>` or `<a>`. Configure with `data-color`, `data-variant`, `data-size` — all optional, all independent and combinable.

```html
<!-- Color (default: primary) -->
<button class="button">Primary</button>
<button class="button" data-color="secondary">Secondary</button>
<button class="button" data-color="danger">Danger</button>
<button class="button" data-color="inherit">Inherit</button> <!-- pulls palette from parent <color-mode> -->

<!-- Variant (default: filled) -->
<button class="button" data-variant="outlined">Outlined</button>
<button class="button" data-variant="plain">Plain</button>

<!-- Size (default: medium) -->
<button class="button" data-size="small">Small</button>
<button class="button" data-size="large">Large</button>
```

Only `filled` / `outlined` / `plain` variants exist — there is **no** `data-variant="tinted"` despite it appearing in some Varde docs (see `pitfalls.md`).

With an icon or spinner (see below for available icons):

```html
<button class="button">
  <span class="icon" data-icon="plus"></span>
  Add item
</button>

<button class="button" aria-label="Add"> <!-- icon-only buttons need aria-label -->
  <span class="icon" data-icon="plus"></span>
</button>

<button class="button">
  <span class="spinner"></span>
  Saving…
</button>
```

There is no `:disabled` visual style by design — a spinner communicates "in progress" instead of graying the button out.

Shape utilities layer on top since button styles use low-specificity selectors:

```html
<button class="button br-circle" aria-label="Add"><span class="icon" data-icon="plus"></span></button>
<button class="button br-pill" data-variant="outlined">Filter</button>
```

`.button` works identically on `<a>`:

```html
<a href="/settings" class="button" data-variant="outlined">Settings</a>
```

## Form controls

Apply `.input`, `.select`, `.textarea`, `.checkbox`, `.radio` to their native elements. Pair with `.form-label` for labels (muted color, small size, medium weight). Size with `data-size="small"|"large"` (medium is default). Mark errors with `aria-invalid="true"` — no custom error classes needed, border/text turn red automatically.

```html
<label class="form-label" for="name">Full name</label>
<input class="input" type="text" id="name" placeholder="e.g. Ola Nordmann" />

<label class="form-label" for="message">Message</label>
<textarea class="textarea" id="message" rows="3" placeholder="Write something…"></textarea>

<label class="form-label" for="country">Country</label>
<select class="select" id="country">
  <option value="">Choose…</option>
  <option value="no">Norway</option>
</select>

<div class="stack-horizontal gap-s">
  <input class="checkbox" type="checkbox" id="terms" />
  <label for="terms">I accept the terms</label>
</div>
```

Always pair checkbox/radio with a real `<label>` — the label text isn't built into the control.

Error state — wrap the message in `<color-mode palette="coral">` to match Varde's error coloring convention (see `page-shell.md`):

```html
<label class="form-label" for="email">Email</label>
<input class="input" type="email" id="email" value="not-an-email" aria-invalid="true" />
<color-mode palette="coral">
  <p class="fs-xs t-medium mt-2xs">Please enter a valid email address</p>
</color-mode>
```

Disabled controls get `cursor: not-allowed`, no graying out — if you need to communicate unavailability, prefer an explanation over a dimmed appearance.

## Table

`.table` (or a plain `<table>` inside `.typeset` — both are styled identically). Control density with `data-density`.

```html
<table class="table">
  <thead>
    <tr><th>Consultant</th><th>Client</th><th>Status</th></tr>
  </thead>
  <tbody>
    <tr><td>Emma Berg</td><td>Acme Corp</td><td>Active</td></tr>
  </tbody>
</table>

<table class="table" data-density="compact">…</table>
<table class="table" data-density="relaxed">…</table>
```

`<caption>` renders below the table, muted and small.

## Link

Plain `<a>` tags outside `.typeset` render unstyled by default (no underline/color) — add `.link` explicitly, or rely on `.typeset` to style any link inside it automatically.

```html
<a class="link" href="/docs">Documentation</a>
```

## Icon

`.icon` + `data-icon`. **Only three icons are actually shipped: `plus`, `pencil`, `cage`.** Do not invent other `data-icon` values — anything else renders as an empty box. If a specific icon is genuinely needed and isn't one of these three, use a plain inline `<svg>` instead of `.icon`/`data-icon`.

```html
<span class="icon" data-icon="plus"></span>
<span class="icon" data-icon="pencil"></span>
<span class="icon" data-icon="cage"></span>
```

Icons inherit color from their parent (`currentColor`) and scale with font size.

## Spinner

```html
<span class="spinner"></span>
```

Customizable via CSS custom properties set on the element: `--spinner-size` (default `1.25lh`), `--spinner-color` (default `currentColor`), `--spinner-speed` (default `1s`).
