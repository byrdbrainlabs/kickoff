# Interactions Schema

This is the canonical structure and convention reference for an Interactions document. Read this whenever you need to write, update, or consume one — do not invent structure or conventions.

An interactions document specifies the product's **global behavior**: animation principles, global input handling, canonical state-transition patterns, navigation, feedback, and responsive behavior. It does NOT specify visual appearance — that lives in the Design System. It also does NOT specify **feature-local** interactions (a bespoke gesture or shortcut belonging to one capability) — those live in that capability's feature doc.

This is a **foundation-tier, living** document: the design agent establishes the global principles and defaults at kickoff, and extends it over time as tickets surface genuinely *global* patterns. Feature-specific interaction detail rides in tickets and lands in feature docs.

**Produced and maintained by:** `agents/design.md`
**Consumed by:** the build process (your coding harness), `agents/feature.md`, `agents/documentation.md`
**File location:** `docs/interactions.md`

---

## Sections

A complete interactions document contains the following sections, in order:

1. Animation & Transition Principles
2. Keyboard Shortcuts
3. Mouse & Touch Interactions
4. State Transition Patterns
5. Focus & Navigation
6. Feedback Patterns
7. Responsive Behavior

### 1. Animation & Transition Principles

Define the rules governing motion in the product:

- **Duration tokens** — named durations (e.g., `instant` 0ms, `fast` 150ms, `normal` 250ms, `slow` 400ms)
- **Easing functions** — named curves (e.g., `ease-out` for entrances, `ease-in` for exits, `ease-in-out` for movement)
- **What animates** — which property categories animate by default (opacity, transform), which don't (layout-shifting properties)
- **Reduced-motion behavior** — what happens when the user prefers reduced motion

**Completion:** A developer can pick the right duration and easing for any new animation without inventing values.

### 2. Keyboard Shortcuts

List every keyboard shortcut supported, organized by domain. For each:

- **Binding** — the key combination (use platform-neutral notation, e.g., `Cmd/Ctrl+P`)
- **Action** — what it does
- **Scope** — global, or limited to a specific surface

Group by domain: file operations, editor, navigation, chat, etc. Avoid duplicates across scopes unless behavior differs.

**Completion:** Every interactive action that should be keyboard-accessible has a binding.

### 3. Mouse & Touch Interactions

Specify **global** non-trivial pointer interactions — patterns reused across more than one capability (a standard drag-and-drop affordance, a context-menu convention). Skip standard click-on-button behavior. A pointer interaction unique to a single capability is feature-local — it lives in that capability's feature doc, not here.

For each pattern, cover:

- **Input** — single click, double click, right-click, drag, gesture
- **Target** — which UI surface accepts the input
- **Feedback** — visual indication during the action (cursor change, drop zone highlight, etc.)
- **Result** — what happens on completion
- **Unsupported variants** — what explicitly does NOT work, if there's a reasonable assumption otherwise

Common patterns to address: drag-and-drop, context menus, text selection behaviors, multi-select, hover-reveal controls.

**Completion:** Every global non-trivial pointer interaction has a specified pattern. Feature-local pointer interactions are specified in their feature docs.

### 4. State Transition Patterns

Define the standard treatments for the four canonical states. For each, specify what the user sees, how long it shows, and the recovery path:

- **Loading** — when the wait is < 200ms (no indicator), 200ms–2s (spinner or skeleton), > 2s (progress + cancel)
- **Empty** — first-run / no-data states, with call to action
- **Error** — error display hierarchy (inline / toast / modal), retry / recovery options
- **Success / confirmation** — when shown, where, dismissal rules

Also specify:

- **Optimistic updates** — when the UI assumes success before confirmation, and how it reconciles on failure
- **Skeleton vs. spinner** — when each is appropriate

**Completion:** A developer building a new feature can choose the correct state treatment without inventing one.

### 5. Focus & Navigation

Specify keyboard and screen-reader navigation rules:

- **Tab order** — how focus moves through major UI surfaces
- **Focus visibility** — when the focus ring shows (always, only on keyboard input, etc.)
- **Focus trap rules** — for modals, popovers, command palettes
- **Default focus** — where focus lands on each surface mount
- **Escape behavior** — what Escape closes/cancels at each level

Visual appearance of focus rings is in `design-system.md`. Navigation order and trap behavior is here.

**Completion:** A keyboard-only user can reach every interactive element in the product.

### 6. Feedback Patterns

Define how the product communicates status to the user beyond state changes:

- **Tooltips** — show delay, hide delay, placement rules, when they're required vs. optional
- **Toasts / notifications** — placement, stacking, dismissal (auto vs. manual), timing
- **Confirmation dialogs** — when required (destructive actions, irreversible operations), format, default button placement
- **Banners** — when persistent feedback is appropriate (offline mode, etc.)
- **Inline validation** — when errors show (on blur, on submit, real-time)

**Completion:** Every "tell the user something" need in the product has a chosen pattern.

### 7. Responsive Behavior

If the product is responsive, specify what happens at each breakpoint. Breakpoint *thresholds* live in `design-system.md`; the *behavior* at each threshold lives here.

For each breakpoint:

- **Panel collapse rules** — what hides, what collapses, what stacks
- **Adaptive UI** — controls that change form (e.g., menu → drawer)
- **Minimum viable size** — below which the app refuses to render or shows a "screen too small" state

Skip this section if the product has a fixed minimum size (typical for desktop apps).

**Completion:** A developer testing at every breakpoint knows what to expect.

---

## What belongs in Interactions vs. Design System vs. Feature docs vs. Foundation vs. coding harness

The interactions document covers **global behavior only**. The following belong elsewhere:

| Concern | Belongs in |
|---|---|
| Animation duration, easing, what animates | Interactions |
| Keyboard shortcut bindings (global) | Interactions |
| **Global** drag-and-drop flow and feedback | Interactions |
| State patterns (loading / empty / error / success) | Interactions |
| Focus navigation order and trap rules | Interactions |
| **Feature-local** interaction (a gesture/shortcut for one capability) | the capability's feature doc |
| Specific color values | Design System |
| Component visual appearance | Design System |
| Focus ring visual style | Design System |
| Breakpoint thresholds | Design System |
| WCAG conformance level | Foundation › Platform & Constraints |
| Performance budgets the user can perceive (60fps target, latency) | Foundation › Platform & Constraints |
| Supported platforms | Foundation › Platform & Constraints |
| Animation library (framer-motion etc.) | Coding harness |
| Keyboard event handling framework | Coding harness |
| Accessibility implementation (ARIA patterns, focus-management libs) | Coding harness |
| Gesture-handling library | Coding harness |

If the user volunteers a user-facing commitment (WCAG level, performance target, supported platforms), note it for the foundation and recommend re-running the kickoff agent (or a ticket's Platform & Constraints Amendment). If the user volunteers an implementation choice, the coding harness decides — acknowledge and do not capture.

---

## Sensible Defaults

These are the values the producing agent should use when the user has not explicitly specified. Every defaulted value in the output should be marked with `<!-- default -->` so a future reviewer knows what was assumed versus stated.

### Animation & transitions

- **Durations:** `instant` 0ms, `fast` 150ms, `normal` 250ms, `slow` 400ms
- **Easing:** `ease-out` for entrances / reveals, `ease-in` for exits / dismissals, `ease-in-out` for movement between states
- **What animates:** opacity, transform. Layout-shifting properties (height/width/margin) do not animate by default.
- **Reduced motion:** Disable non-essential motion. Replace transitions with instant state changes. Keep critical feedback (toast appearance, error indication).

### Keyboard shortcuts (standard set, always present unless overridden)

- `Cmd/Ctrl+S` — Save
- `Cmd/Ctrl+Z` — Undo
- `Cmd/Ctrl+Shift+Z` or `Cmd/Ctrl+Y` — Redo
- `Esc` — Dismiss / cancel innermost overlay
- `Tab` / `Shift+Tab` — Forward / back through focusable elements
- `Cmd/Ctrl+,` — Settings (if applicable)
- `Cmd/Ctrl+P` — Quick navigation / fuzzy finder (if applicable)

Bespoke shortcuts beyond this set go in the Keyboard Shortcuts section of the doc.

### State transitions

- **Loading thresholds:**
  - `< 200ms`: no indicator
  - `200ms–2s`: spinner or skeleton
  - `> 2s`: progress indicator with cancel option
- **Empty states:** Brief description + a call-to-action button.
- **Error display hierarchy:**
  - Inline (field-level validation)
  - Toast (transient operational errors, with retry)
  - Modal (critical errors requiring acknowledgment)
- **Success / confirmation:** Toast for transient confirmation; inline indicator for in-place results.
- **Optimistic updates:** For low-risk operations (favorites, likes, simple toggles). For destructive or high-risk operations, wait for confirmation.

### Focus & navigation

- **Tab order:** Follows visual reading order (top-to-bottom, left-to-right).
- **Focus visibility:** Show focus ring on keyboard input; suppress on mouse click (`:focus-visible` semantics).
- **Focus trap:** Standard for modals, popovers, and command palettes. Escape closes the innermost trap.
- **Default focus on mount:**
  - Modal → primary action
  - Form → first input
  - Command palette → search input

### Feedback patterns

- **Tooltips:** 500ms show delay, 0ms hide delay, placement above the element with fallback to below.
- **Toasts:** Bottom-right (mobile: bottom-center). 4s auto-dismiss for success / info; persistent until manually dismissed for error.
- **Confirmation dialogs:** Required for destructive or irreversible actions. Format: title + body explaining consequences + secondary "Cancel" + primary destructive action. Default focus on Cancel.
- **Inline validation:** On blur for required fields; real-time for format-sensitive fields (email, password strength).

### Responsive behavior

- **Minimum width before split-panel collapse:** 768px.
- **Below minimum:** Single-column stacked layout.

---

## Conventions

### Cross-references

Reference UI elements by the exact names defined in the foundation's Terminology and Experience Overview (and feature docs' Terminology for feature-local elements). Reference design tokens by the exact names defined in `design-system.md` (e.g., `space-2`, `accent`).

### Specificity

Be concrete. "Show a spinner during load" is too vague. "Show the spinner after 200ms of waiting; replace with the result on completion or with an inline error with retry button on failure" is right.

### Ambiguity markers

`[NEEDS CLARIFICATION: <specific question>]` — place inline wherever a behavior is incomplete. Downstream agents refuse to proceed past unresolved markers.

### Grounding in the product

Every global interaction pattern documented here should serve at least one scenario or feature that exists in the foundation, a ticket, or a feature doc. Do not invent patterns for behavior the product doesn't have.

---

## Output skeleton

````markdown
# Interactions

_<one-line summary of the interaction philosophy>_

---

## Animation & Transition Principles

### Duration Tokens
| Token | Value | Usage |
|---|---|---|
| `instant` | 0ms | … |
| `fast` | 150ms | … |

### Easing Functions
| Token | Curve | Usage |
|---|---|---|
| `ease-out` | … | Entrances |

### Reduced Motion
…

## Keyboard Shortcuts

### <Domain>
| Binding | Action | Scope |
|---|---|---|
| `Cmd/Ctrl+P` | Open fuzzy finder | Global |

## Mouse & Touch Interactions

### <Pattern Name>
- **Input:** …
- **Target:** …
- **Feedback:** …
- **Result:** …

## State Transition Patterns

### Loading
…

### Empty
…

### Error
…

### Success / Confirmation
…

## Focus & Navigation

- **Tab order:** …
- **Focus visibility:** …
- **Focus trap rules:** …
- **Escape behavior:** …

## Feedback Patterns

### Tooltips
…

### Toasts
…

### Confirmation Dialogs
…

## Responsive Behavior

### <Breakpoint Name>
…
````
