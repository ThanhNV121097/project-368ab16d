# Design System — Hello Word

> Source of truth: the approved `index.html` (preview: http://localhost:8080/design/368ab16d-a7b8-4230-9647-28411080e6e9).
> Every value below is extracted from it. Changing a value here without
> changing the approved design is a defect.

Last updated: 2026-05-27

## 1. Foundations

### 1.1 Color

Semantic tokens. Name by job, never by hue.

| Token | Value | Used for |
|---|---|---|
| `--color-bg` | `#FFFFFF` | Page background |
| `--color-surface` | `#F9FAFB` | Card background |
| `--color-surface-hover` | `#EFF6FF` | Button hover background (accent tint) |
| `--color-border` | `#E5E7EB` | Default border, divider |
| `--color-text` | `#111827` | Body text, greeting text |
| `--color-text-muted` | `#6B7280` | Eyebrow, hint, empty-state text |
| `--color-primary` | `#2563EB` | Primary action / accent |
| `--color-primary-text` | `#FFFFFF` | Text on primary (pressed button) |
| `--color-danger` | `#DC2626` | Error state text |
| `--color-focus` | `#2563EB` | Focus ring (same hue as primary) |

No success or warning color exists in the approved design — omit them until a
state needs one.

#### Contrast audit

Every text-on-background pair actually used. Body text ≥ 4.5:1, large text (≥ 18.66px bold or ≥ 24px) ≥ 3:1.

| Foreground | Background | Ratio | Passes |
|---|---|---|---|
| `--color-text` `#111827` | `--color-bg` `#FFFFFF` | `17.7:1` | AAA |
| `--color-text` `#111827` | `--color-surface` `#F9FAFB` | `17.0:1` | AAA |
| `--color-text-muted` `#6B7280` | `--color-surface` `#F9FAFB` | `4.6:1` | AA |
| `--color-primary` `#2563EB` | `--color-bg` `#FFFFFF` | `5.2:1` | AA |
| `--color-primary` `#2563EB` | `--color-surface-hover` `#EFF6FF` | `4.7:1` | AA |
| `--color-primary-text` `#FFFFFF` | `--color-primary` `#2563EB` | `5.2:1` | AA |
| `--color-danger` `#DC2626` | `--color-surface` `#F9FAFB` | `4.6:1` | AA |

`--color-border` `#E5E7EB` on `--color-bg` measures `1.2:1` and on
`--color-surface` `1.2:1` — below the 3:1 UI-border guideline. Recorded under
Known deviations; the border carries no information on its own, so this does
not block usability.

### 1.2 Spacing

Base unit: `4px`. Every margin, padding, and gap in the product uses one of these.

| Token | Value | Used for |
|---|---|---|
| `--space-2` | `8px` | Button row gap |
| `--space-4` | `16px` | Eyebrow bottom margin; focus outline offset |
| `--space-5` | `20px` | Hint top margin |
| `--space-6` | `24px` | Page body padding |
| `--space-8` | `32px` | Card horizontal padding; preview nav top margin |
| `--space-10` | `40px` | Card padding (mobile) |
| `--space-14` | `56px` | Card vertical padding |

Button horizontal padding `14px` (in `--space-4`-adjacent territory but not a
multiple of 4) is a deviation — see Known deviations.

### 1.3 Typography

Font family — single system stack, no webfont load:

- Body/headings: `system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif`
- Mono: none used.

| Token | Size | Line height | Weight | Used for |
|---|---|---|---|---|
| `--text-2xs` | `12px` | 1.4 | 400 | Eyebrow (uppercase, `letter-spacing: .12em`) |
| `--text-xs` | `13px` | 1.4 | 400 | Preview button label |
| `--text-sm` | `14px` | 1.4 | 400 | Hint text |
| `--text-display` | `40px` | 1.15 | 700 | Greeting headline (h1) |
| `--text-display-sm` | `32px` | 1.15 | 700 | Greeting headline (mobile) |

Weights used: 400 (default), 500 (empty-state greeting), 700 (greeting).
One heading level exists (h1) — no level is skipped.

### 1.4 Radius, border, shadow, motion

| Token | Value | Used for |
|---|---|---|
| `--radius-md` | `16px` | Card |
| `--radius-full` | `9999px` | Preview button pill |
| `--border-width` | `1px` | Card border, button border |
| `--shadow-sm` | `0 1px 2px rgba(17,24,39,.05)` | Resting card |
| `--duration-fast` | `150ms` | Button background / color / border-color |
| `--duration-base` | `180ms` | Greeting state opacity |
| `--easing` | `ease` | All transitions |

No `prefers-reduced-motion: reduce` rule appears in the approved design —
recorded under Known deviations. Motion is limited to a short opacity/color
crossfade and the hover fill; the page itself never moves.

### 1.5 Layout and breakpoints

| Name | Max width | Container | Columns | Gutter |
|---|---|---|---|---|
| `base` | — | card `min(480px, 100%)`, centered by flex | 1 | — |
| `mobile` | `420px` | card padding shrinks to `40px 20px`; greeting `32px` | 1 | — |

Z-index scale (only these values are allowed):

| Layer | Value |
|---|---|
| Base | `0` |

No overlapping or floating layers exist, so no higher z-index is defined yet.

## 2. Components

One subsection per reusable component. Every component lists **all** states.

### 2.1 GreetingCard

**Purpose** — the single screen of the app: shows one greeting line read from
PostgreSQL plus a helper hint. The only content-bearing surface; not for
navigation or forms.

**Anatomy** — `[eyebrow] [greeting h1] [hint]`, stacked, centered, inside a
bordered surface card. Optional preview nav (below the card, outside it) is a
separate component (2.2).

**Variants** — none. One layout at two breakpoints (see 1.5).

**Sizes** — one: `width min(480px, 100%)`, `padding 56px 32px` (mobile `40px 20px`).

**States** — the greeting line itself carries four states via `data-state`;
the card shell is constant.

| State | Visual change | Tokens |
|---|---|---|
| Default / loaded | Greeting `#111827`, weight 700, full opacity | `--color-text`, `--text-display` |
| Hover | None (card is not interactive) | — |
| Focus (keyboard) | None on the card itself; the card is `aria-live` but not focusable | — |
| Active / pressed | None | — |
| Disabled | N/A — no controls in the card | — |
| Loading | Greeting opacity `.35`, text `…`; hint "Reading from PostgreSQL…" | `--duration-base`, `--color-text` |
| Error | Greeting `#DC2626`, text "Could not load greeting"; hint "The database did not respond." | `--color-danger` |
| Empty | Greeting `#6B7280` weight 500, text "No greeting set"; hint "The table is empty." | `--color-text-muted` |

**Accessibility** — `main` landmark wraps the card; `aria-live="polite"` so
state changes are announced. The greeting is an `h1` and is the page's single
heading. States are driven by the `data-state` attribute, which the real
implementation must keep in sync with the visible text.

### 2.2 PreviewStateButton

**Purpose** — a demo-only control to flip the greeting card between its four
states during design review. Not part of the shipped product.

**Anatomy** — `[label]`, pill-shaped button.

**Variants**

| Variant | Tokens | When to use |
|---|---|---|
| Idle | `--color-primary` text, `--color-bg` fill, `--color-border` | State not currently selected |
| Pressed | `--color-primary` fill, `--color-primary-text` text, `--color-primary` border | State currently selected (`aria-pressed="true"`) |

**Sizes** — one: `font-size 13px`, `padding 8px 14px`.

**States** — every row must be filled in.

| State | Visual change | Tokens |
|---|---|---|
| Default | Accent text on white, pill border | `--color-primary`, `--color-bg`, `--color-border`, `--radius-full` |
| Hover | Fill `#EFF6FF`, border `#2563EB` | `--color-surface-hover`, `--color-primary` |
| Focus (keyboard) | `outline: 2px solid var(--accent)`, `outline-offset: 2px` | `--color-focus` |
| Active / pressed | Fill `#2563EB`, text `#FFFFFF`, border `#2563EB` | `--color-primary`, `--color-primary-text` |
| Disabled | Not defined (demo control; never disabled) | — |
| Loading | N/A | — |
| Error | N/A | — |
| Empty | N/A | — |

**Accessibility** — real `<button type="button">` elements; the selected state
is exposed via `aria-pressed`. Keyboard focus uses `:focus-visible` with a
visible outline (never removed). Hit target `34×32px` (padding 8px×14px at
13px text) is below the 44×44px guideline — the control is demo-only and sits
at the bottom of a single-screen review page, recorded under Known deviations.

## 3. Content and formatting

- **Voice and tone** — plain, literal, one-line. No marketing copy.
- **Formats** — no dates, times, numbers, or currency appear.
- **Capitalization** — sentence case everywhere; the eyebrow is uppercase
  *via CSS* (`text-transform: uppercase`), its source text is sentence case.
- **Empty-state wording** — "No greeting set" + "The table is empty." — states
  what is missing and where to look.
- **Error-state wording** — "Could not load greeting" + "The database did not
  respond." — states the failure and its cause without exposing internals.

## 4. Known deviations

| Where | Deviation | Why it stands | Follow-up |
|---|---|---|---|
| Color | `--color-border` `#E5E7EB` contrast `1.2:1` on bg/surface | Below the 3:1 UI-border guideline | Non-informational border; leave unless borders start carrying state |
| Spacing | Button horizontal padding `14px` not a multiple of the 4px base | One-off value in approved CSS | Normalize to `16px` only if the button ships |
| Spacing | Card padding `56px 32px` (`--space-14`/`--space-8`) is generous for one line | Oversized-padding default (ai-defaults #4) | Stakeholder approved it; single-screen showcase |
| Motion | No `prefers-reduced-motion: reduce` rule | Motion is a short crossfade/hover fill, no movement | Add the rule if transitions grow beyond opacity/color |
| Radius | `border-radius: 9999px` on the preview button | Pill used for a control, not a badge/avatar (ai-defaults #3) | Demo-only control; avoid pill buttons in shipped UI |
| Accessibility | Preview button hit target `34×32px` < 44×44px | Demo control at page bottom | Does not ship; use ≥44px for any real control |

The approved design otherwise avoids the ai-defaults list: no purple/indigo
(accent is a single reasoned blue), no decorative gradients, flat fills, one
structural radius (16px) plus one pill, a light resting shadow only, no emoji
iconography, and realistic copy including loading/error/empty strings.

## 5. Change log

| Date | Change | Design PR |
|---|---|---|
| 2026-05-27 | Initial design system extracted from approved `index.html` | — |
