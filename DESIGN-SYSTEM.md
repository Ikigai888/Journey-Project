# Journey — Design System v0.1

Single source of truth for the visual language shared by the waitlist landing
page and the product concept prototype. Lives in `design-tokens.css` — every
other file reads color, type, and spacing from there instead of hardcoding
its own values.

## What changed (Aug 12, 2026)

Colour pass for empathy. An audit found 10 WCAG AA contrast failures, and they
were concentrated in exactly the copy that carries the most care — the safety
note about professional support sat at 2.2:1, the least visible text on the
page. Two root causes, both now fixed:

- Bright `--accent` was being used as small text (every eyebrow, every `h3`,
  the wayline label) at ~2.6:1. Gold now splits by role — it fills and marks,
  `--accent-deep` speaks. The palette is unchanged; only the readable step of
  it is used for type.
- The text neutrals were cool blue-grays on a warm paper canvas. They are now
  warm taupes derived from the surface hue, and `--text-dimmer` was darkened
  enough to actually be read.

Nothing about the identity moved: same gold, same warm paper, same three
faces. Every text pair on every page now clears AA.

## What changed (Jul 24, 2026)

The original landing page used a dark-to-light "arc" — hero starts near-black,
each section lightens toward the signup form. Dropped, per direction: no
dark/light concept for this project. Everything now runs on one flat, warm
palette. `--ink` is reserved for chrome elements (the prototype's sidebar,
button fills) — it is no longer a section background.

The landing page and the prototype had already converged on nearly the same
values independently (Newsreader + IBM Plex Mono + Inter, a warm paper
background, the same gold accent) — this just makes that convergence
explicit and enforced in one file instead of two.

## Files

- `design-tokens.css` — colors, type families/scale, spacing, radii. Edit
  values here; nothing else.
- `base.css` — shared element styles and components (buttons, cards, pills,
  forms, the wayline element) built on top of the tokens.
- `index.html` — the waitlist page. Links both CSS files, adds
  page-specific layout on top.
- `journey-prototype.html` — the product concept walkthrough. Links
  `design-tokens.css` and aliases its existing (shorter) variable names to
  the shared tokens, so its own component CSS didn't need a full rewrite.
  Its viewer-only chrome (tabs, frame) keeps a few local one-off grays —
  those aren't part of the shared system, they're scaffolding for viewing
  the prototype itself.

## Tokens at a glance

**Color — surfaces:** `--paper` (primary bg), `--paper-1` (alt section band),
`--paper-2` (deepest neutral, used sparingly), `--surface` (cards/inputs,
white), `--line` / `--line-soft` (borders).

**Color — ink:** `--ink` / `--ink-1` — reserved for chrome (nav sidebar,
solid button fills), not page backgrounds.

**Color — text:** `--text`, `--text-dim`, `--text-dimmer`, `--text-on-ink`.
Warm neutrals, not cool gray — they sit on warm paper, and a blue-cast gray
reads clinical against it. All three clear WCAG AA (4.5:1) on both `--paper`
and `--paper-1`.

**Color — accent:** `--accent` (wayline gold, `#c8912b`), `--accent-soft`,
`--accent-deep` (`#8a6317`). Split by role: **`--accent` fills and marks**
(button, wayline track and dot, list bullets); **`--accent-deep` speaks** —
use it anywhere gold has to be read as text or act as a focus ring. Bright
gold is only 2.6:1 on paper: right as a fill, illegible as a 13px label.

**Color — status:** `--ok`, `--error`.

**Type families:** `--font-serif` (Newsreader — headlines only),
`--font-mono` (IBM Plex Mono — eyebrows, labels, meta), `--font-sans`
(Inter — everything else).

**Type scale:** `--text-h1` through `--text-micro`, all `rem`-based, `h1`/`h2`
use `clamp()` for responsive sizing without a media query.

**Spacing:** `--space-1` (4px) through `--space-9` (96px) — an 8pt-ish scale,
consistent multiples so margins/padding read as intentional rhythm.

**Radii:** `--radius-sm` (6px, buttons/inputs), `--radius-md` (9px, cards),
`--radius-lg` (14px, the prototype device frame), `--radius-full` (pills).

## Components (`base.css`)

`.eyebrow`, `.lede`, `.btn` / `.btn.ghost`, `.card`, `.pill`, form elements
(`label`, `input`, `select`, `textarea`, `.form-status`), and `.wayline` —
the signature progress-track element used on both the landing page (as a
static teaser) and inside the prototype (as a live "you are here" indicator
with phase marks).

## Extending this

When the product moves into an actual codebase (React/Tailwind or otherwise),
`design-tokens.css` is the file to port first — translate each custom
property into that framework's token format (Tailwind config, CSS-in-JS
theme object, etc.) directly, not by eyeballing colors off a screenshot.
That's the point of centralizing it now.
