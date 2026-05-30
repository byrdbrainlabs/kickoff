# Design System Schema

This is the canonical structure and convention reference for a Design System document. Read this whenever you need to write, update, or consume a design system — do not invent structure or conventions.

A design system specifies the product's **global visual language**: visual identity, color, typography, spacing, layout, iconography, and the appearance of **shared, reusable components**. It does NOT specify behavior, animation, or interaction patterns — those live in the Interactions document. It also does NOT specify **feature-local** components (UI that belongs to one capability) — those live in that capability's feature doc.

This is a **foundation-tier, living** document: the design agent establishes the global tokens at kickoff (mostly defaulted) and extends it over time as tickets surface genuinely *global* components. Feature-specific design rides in tickets and lands in feature docs.

**Produced and maintained by:** `agents/design.md`
**Consumed by:** the build process (your coding harness), `agents/feature.md`, `agents/documentation.md`
**File location:** `docs/design-system.md`

---

## Sections

A complete design system contains the following sections, in order:

1. Brand & Visual Direction
2. Color
3. Typography
4. Spacing & Layout
5. Iconography
6. Component Visual Specifications
7. Theming
8. Imagery & Illustration (optional)
9. Asset Requirements

### 1. Brand & Visual Direction

A short description (1–3 paragraphs) of the visual feel and brand intent: mood, voice in visual form, reference inspirations, and what the product should evoke. Reference any "looks like X but with Y" comparisons.

**Completion:** A designer or developer can describe the intended aesthetic in one sentence after reading this section.

### 2. Color

Define every color the product uses, organized into:

- **Theme variants** — at minimum a default theme; light/dark if both are supported
- **Surface tokens** — background, foreground (text), border, elevation surfaces
- **Semantic tokens** — success, error, warning, info, focus
- **Brand tokens** — primary, secondary, accent

For each color, specify:
- The token name (e.g., `surface-1`, `text-primary`, `accent`)
- The value (hex, HSL, or both)
- Per-theme value if it differs

**Completion:** Every color a developer needs to use has a named token; no UI surface requires inventing a color.

### 3. Typography

Define:

- **Font families** — display, body, mono (and any other roles), with fallback stacks
- **Type scale** — named sizes (e.g., `xs`, `sm`, `body`, `lg`, `h2`, `h1`) with px or rem values
- **Line heights** — per scale step
- **Font weights** — which weights are available and which roles they map to (e.g., headings: 600, body: 400)
- **Letter spacing** (if non-default)

**Completion:** Every text role in the product has a defined type token.

### 4. Spacing & Layout

Define:

- **Base unit** — typically 4px or 8px
- **Spacing scale** — named tokens (e.g., `space-1` = 4px, `space-2` = 8px, etc.)
- **Layout dimensions** — fixed sizes for major UI areas (e.g., sidebar width, header height)
- **Breakpoints** — if responsive, the screen-width thresholds (visual definitions only; behavior at breakpoints is in `interactions.md`)
- **Container max-widths** — if applicable

**Completion:** Every spacing decision in the product can reference a named token.

### 5. Iconography

Define:

- **Icon style** — the stylistic family (e.g., "Lucide-style outline icons, 1.5px stroke")
- **Default size** and any size variants
- **Color application** — when icons inherit text color vs. use semantic tokens

The choice of specific icon library (the NPM package, the engine) is the coding harness's call. The style direction is here.

**Completion:** Any developer adding a new icon knows what it should look like.

### 6. Component Visual Specifications

Specify the appearance of the **global, shared components** — UI elements reused across more than one capability (buttons, inputs, cards, dialogs, toasts, the persistent UI areas named in the foundation's Experience Overview). Cover the shared components the product actually uses — not a generic exhaustive library.

**Scope test (global vs. feature-local):** if a component is used by more than one feature, or is a persistent UI area from the foundation, it is global and belongs here. If it belongs to a single capability (e.g., a resource-management mini-window, a category picker), it is feature-local — it does **not** go here; it lives in that capability's feature doc. The design agent makes this call by the reuse heuristic when a ticket surfaces it.

For each global component:

- **Default appearance** — fill, border, text color, typography, padding, corner radius, elevation
- **Visual states** — hover, active, disabled, focused, selected. Specify what changes (color, border, shadow, etc.) — not what animates.
- **Variants** — e.g., primary / secondary / ghost for buttons; small / medium / large for sizes
- **Composition rules** — internal spacing, alignment, icon placement

**Completion:** Every shared component and every persistent UI area from the foundation has a visual spec here. Feature-local components are specified in their feature docs, not here.

### 7. Theming

Specify:

- **Available themes** — which themes the product supports
- **Default theme**
- **How tokens map across themes** — typically the same token name, different value per theme
- **Surface elevation** — if applicable, how surfaces stack visually across themes

Theme switching *behavior* (where the toggle lives, transition animation) is an interaction concern — note it but defer to `interactions.md`.

**Completion:** A developer building a new component knows how it should appear in every supported theme.

### 8. Imagery & Illustration (optional)

If the product uses imagery or illustrations:

- **Style direction** — photographic, illustrated, abstract, mixed
- **Treatment rules** — borders, corner radius, overlays, filters
- **Empty-state illustration** style — if any

Skip this section if the product has no significant imagery.

### 9. Asset Requirements

List concrete assets the build needs:

- **Fonts** — files to include, weights, formats
- **Application icon** — sizes, formats per platform
- **Empty-state and onboarding illustrations** — if any
- **Logos** — variants needed

**Completion:** A developer knows what to add to the repo before building.

---

## What belongs in the Design System vs. Interactions vs. Feature docs vs. Foundation vs. coding harness

The design system covers **global appearance only**. The following belong elsewhere:

| Concern | Belongs in |
|---|---|
| Specific color values, type scale, spacing tokens | Design System |
| **Global, shared** component visual specs and states | Design System |
| Theme color values | Design System |
| Breakpoint thresholds (the screen widths) | Design System |
| Focus *visual* (the ring style) | Design System |
| **Feature-local** component visual specs | the capability's feature doc |
| Animation timing, easing, what animates | Interactions |
| Keyboard shortcuts | Interactions |
| Drag-and-drop behavior | Interactions |
| Loading / empty / error state patterns | Interactions |
| Behavior at breakpoints (panel collapse rules) | Interactions |
| Focus *navigation order* (tab order, focus trap) | Interactions |
| WCAG conformance level | Foundation › Platform & Constraints |
| Performance commitments the user can perceive (60fps target, latency) | Foundation › Platform & Constraints |
| Supported platforms / browsers / OS versions | Foundation › Platform & Constraints |
| Component library choice (Radix, MUI, etc.) | Coding harness |
| CSS framework choice (Tailwind, vanilla) | Coding harness |
| Design-token format / CSS variables / build mechanics | Coding harness |
| Specific icon library NPM package | Coding harness |
| Animation library (framer-motion etc.) | Coding harness |

If the user volunteers a user-facing commitment (WCAG level, supported platforms, performance target), note it for the foundation and recommend re-running the kickoff agent (or capturing it via a ticket's Platform & Constraints Amendment). If the user volunteers an implementation choice, the coding harness decides — acknowledge and do not capture.

---

## Sensible Defaults

These are the values the producing agent should use when the user has not explicitly specified. Every defaulted value in the output should be marked with `<!-- default -->` so a future reviewer knows what was assumed versus stated.

### Spacing

- **Base unit:** 4px
- **Scale:** `space-1` 4px, `space-2` 8px, `space-3` 12px, `space-4` 16px, `space-5` 24px, `space-6` 32px, `space-7` 48px, `space-8` 64px

### Typography

- **Scale ratio:** 1.250 (major third)
- **Body size:** 16px (`body`)
- **Scale:** `xs` 12px, `sm` 14px, `body` 16px, `lg` 20px, `h3` 24px, `h2` 32px, `h1` 40px
- **Line heights:** 1.5 for body, 1.2 for headings
- **Weights:** 400 body, 600 headings

### Color (when only mood is specified)

- Generate a palette consistent with the stated mood; ensure body-text contrast meets WCAG AA (4.5:1).
- Default to dark theme if only one is specified.
- Semantic defaults: `success` green, `error` red, `warning` amber, `info` blue, `focus` accent.

### Component visual states

- **hover:** 10–15% lightness shift in the same hue
- **active:** 5–10% darker than default
- **disabled:** 50% opacity; no hover/active states
- **focused:** 2px ring using `focus` (or `accent`) token, 2px offset

### Component visuals

- **Border radius:** 6px for inputs / buttons; 8px for cards / panels
- **Elevation scale:** `elev-1` 1px blur, `elev-2` 4px blur, `elev-3` 12px blur (low-opacity black)

### Iconography

- **Style:** outline icons, 1.5px stroke
- **Default size:** 16px in dense UI, 20px in standard UI, 24px in large UI
- Icons inherit text color unless a semantic token is specified.

### Theming

- If both themes specified: dark is default.
- Same token names across themes; only values differ per theme.

---

## Conventions

### Token naming

Use semantic names, not literal names. Prefer `surface-1` over `gray-50`; `text-primary` over `text-black`; `accent` over `purple-500`. Semantic names survive theme changes and value tweaks.

### Value formats

- Colors: hex (`#RRGGBB`) or HSL. Pick one and use it consistently.
- Sizes: px or rem. Pick one for spacing/typography; consistency matters more than the choice.
- Always pair tokens with the underlying values — never use raw values without a token name in front.

### Ambiguity markers

`[NEEDS CLARIFICATION: <specific question>]` — place inline wherever a visual decision is incomplete. Downstream agents surface all such markers and refuse to proceed past unresolved ones.

### Cross-references

Reference UI elements by the exact names defined in the foundation's Terminology and Experience Overview (and feature docs' Terminology for feature-local elements). Do not invent IDs.

---

## Output skeleton

````markdown
# Design System

_<one-line summary of the visual direction>_

---

## Brand & Visual Direction

<Section 1>

## Color

### <Theme Name>

| Token | Value | Usage |
|---|---|---|
| `surface-1` | `#…` | Primary background |
| `text-primary` | `#…` | Body text |
| … | … | … |

### Semantic

| Token | Value | Usage |
|---|---|---|
| `success` | `#…` | Successful actions, confirmation |
| `error` | `#…` | Errors, destructive actions |
| … | … | … |

## Typography

### Font Families
- **Display:** …
- **Body:** …
- **Mono:** …

### Type Scale

| Token | Size | Line Height | Weight | Usage |
|---|---|---|---|---|
| `h1` | … | … | … | … |
| `body` | … | … | … | … |

## Spacing & Layout

- **Base unit:** …
- **Scale:** `space-1` … `space-N`
- **Breakpoints:** …

## Iconography

- **Style:** …
- **Default size:** …

## Component Visual Specifications

### <Component Name>

- **Default:** …
- **States:** hover, active, disabled, focused …
- **Variants:** …

## Theming

…

## Asset Requirements

…
````
