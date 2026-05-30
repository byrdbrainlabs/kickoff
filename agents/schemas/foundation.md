# Foundation Schema

This is the canonical structure and convention reference for the **Foundation** — the stable, product-wide reference layer established at product inception. Read this whenever you need to write, update, or consume a foundation document — do not invent structure or conventions.

The foundation captures only what is **stable and product-wide**: terminology, voice, platform commitments, experience shape, non-goals, and visual direction. It deliberately does **not** contain scenarios or feature specifications — buildable work lives in **tickets** (`docs/backlog/`) and is consolidated into **feature docs** (`docs/features/`). The foundation is written once at kickoff and amended rarely and deliberately (a ticket may amend it via a Platform & Constraints Amendment).

The foundation is the context every downstream agent inherits without re-asking. It is the largest single lever on coherence: everything else references it.

**Produced by:** `agents/kickoff.md`
**Consumed by:** `agents/feature.md`, `agents/design.md`, `agents/documentation.md`, the build process (your coding harness)
**File location:** `docs/foundation.md`

---

## Sections

A complete foundation contains the following sections, in order:

1. Product Overview
2. Links
3. Terminology
4. Voice & Tone (optional)
5. Platform & Constraints
6. Experience Overview
7. Non-goals
8. Visual Direction (optional)

### 1. Product Overview

A short description (1–3 paragraphs) covering:

- What the application is (web, desktop, mobile, CLI, server)
- Who it's for
- What it helps the user accomplish
- Any similar applications it draws inspiration from

**Completion:** A reader can identify platform, audience, and core value in under 60 seconds.

### 2. Links

Links to peer artifacts:

- Design System (`docs/design-system.md`)
- Interactions (`docs/interactions.md`)
- Feature index (`docs/features/index.md`)
- Backlog (`docs/backlog/`)

### 3. Terminology

The canonical registry of every **core, cross-cutting** concept the product uses — the domain entities, states, temporal rules, and named UI elements that span more than one feature. This section is the **ubiquitous language** for the product: every other artifact (design system, interactions, tickets, feature docs) references these concepts by their Terminology name verbatim — never by synonym, paraphrase, or alternative casing.

Terminology **splits global vs. feature-local**, mirroring design:

- **Cross-cutting concepts** (referenced across multiple features, or fundamental to the product) live here, in the foundation.
- **Feature-local concepts** (introduced by and confined to one capability) live in that capability's **feature doc**, not here. A ticket introduces them in its Terminology section; the documentation agent decides at consolidation whether a term is feature-local (stays in the feature doc) or has become cross-cutting (promote into the foundation — a deliberate amendment).

At kickoff, seed this section with the core entities only. Expect it to grow slowly and deliberately, not with every feature.

```
* **Term**: Brief definition (one to two sentences). When a concept has structure worth naming — multiple states, a temporal rule, a lifecycle event — give the structure its own entry rather than burying it inside another definition.
* **UI Element Name**: What this element does and where it appears.
```

**Completion:** Every cross-cutting concept referenced anywhere in the foundation is defined here and used by its Terminology name verbatim. Core concepts have been pressure-tested for temporal, identity, lifecycle, and boundary semantics (see the kickoff agent's "Semantic completeness" reference). No cross-cutting concept appears in the foundation without an entry.

### 4. Voice & Tone (optional)

How the product talks to the user — button labels, empty states, errors, success messages, and any AI assistant copy. This is product persona, not visual design. The design system and interactions documents reference this section but do not author it.

Include this section only if the product has significant user-facing copy or an AI assistant. Omit entirely for headless/internal products.

Capture:

- **Brand voice:** how the product talks to the user. Adjectives, attitude.
- **Assistant voice** (if applicable): how any in-product AI talks to the user — tone, register, length norms, when it volunteers vs. waits to be asked.
- **Reading level, formality, contractions:** pick concrete defaults.
- **Examples:** at least 3 paired good/bad copy samples for common surfaces (empty state, error, confirmation, button label).

Format:

```
## Voice & Tone

### Brand voice
- <adjective>, <adjective>, <adjective>. <one sentence elaborating>.

### Assistant voice <!-- omit if no AI -->
- <register, length norms, behavioral notes>

### Conventions
- Reading level: <e.g., 8th grade>
- Formality: <casual / neutral / formal>
- Contractions: <yes / no / context-dependent>

### Examples

| Surface | Good | Bad |
|---|---|---|
| Empty state (project list) | "No projects yet — start one." | "There are currently no projects available in your workspace." |
| Error (save failed) | "Couldn't save. Try again?" | "Operation failed due to an unexpected error." |
| Confirm (delete) | "Delete this draft? You can't undo." | "Are you sure you want to perform this destructive operation?" |
```

**Completion:** Brand voice has at least three adjectives, conventions are concrete (not "be friendly"), and there are at least three good/bad pairs.

### 5. Platform & Constraints

The user-observable shape of the product: where it runs, who it serves, what it commits to about availability, data handling, performance, and supported platforms. Everything here is a decision the user can perceive or rely on — not an implementation choice the coding harness makes silently.

Cover every item below that applies. Mark items that don't apply as "Not applicable — <reason>."

- **Application shape.** Web app, desktop, mobile, native CLI, server, hybrid. Single-user, multi-user, or both. Online-required, offline-available, or offline-first.
- **Platform support.** Specific browsers, OS versions, device classes, screen sizes the product commits to working on.
- **Accessibility commitments.** WCAG conformance level (A / AA / AAA). Specific assistive-tech support (screen readers, keyboard-only operation, voice control).
- **Localization.** Languages / locales supported at first release. RTL support. Commitment for adding more locales later.
- **Data handling.** Where the user's data lives (on-device, on-server, both). What leaves the device, when, and to where. Whether the user can export or delete it. Encryption commitments the user can rely on.
- **Telemetry.** What the product collects from the user, what it does not, opt-in or opt-out.
- **Accounts & identity.** Whether the product has accounts. Identity model the user sees (local-only, OAuth providers, magic link). Session lifetime if user-visible.
- **External integrations.** Third-party services surfaced to the user. For each: what the user can do with it, and what they see when it is unavailable.
- **Performance commitments.** Numeric latency, throughput, or capacity targets the user will perceive. Every commitment is a number, not an adjective.
- **Synchronous vs. asynchronous operations.** For any operation that takes long enough to matter (≥ 1s perceived), whether the user waits or it proceeds in the background, and what they see either way.
- **Distribution.** How the user gets the product (app store, web URL, package manager, signed installer). How it updates (auto, prompted, manual).
- **Data preservation across upgrades.** What survives upgrades. What is reversible. What re-install or downgrade loses.

State each item as a **user-facing commitment**, not as implementation. "Works offline" is a commitment; "uses IndexedDB" is implementation and does not go here.

**Completion:** Every applicable item has a concrete answer. Every numeric commitment has a number. Items that do not apply are explicitly marked "Not applicable — <reason>."

### 6. Experience Overview

Describes the overall user experience and application layout. Names and describes the **persistent UI areas** users reference across multiple workflows (e.g., navigation panels, status bars, main content areas). Once named here, these areas can be referenced by name in tickets and feature docs.

**Completion:** Every persistent UI area has a name and 1–2 sentences of description, including visibility / constraint rules where relevant.

### 7. Non-goals

An explicit list of things the product will **not** do, or behaviors it will not support. State each non-goal **positively** ("Conflict resolution is handled in an external Git client") rather than as omission ("we don't handle conflicts"). Positive framing signals deliberate design; negation reads as TODO.

Non-goals prevent the build process from over-building based on inference and stop scope creep at the requirements layer.

**Completion:** At least 3–5 explicit non-goals, or explicit user confirmation that there are none.

### 8. Visual Direction (optional)

A light, non-authoritative statement of the intended visual feel — adjectives, reference products, what it should explicitly NOT look like. This is the **input** to the design agent's foundation pass (which establishes the global tokens); it is **not** a design decision. The design agent may adopt, refine, or set aside any of it.

Include this section only if the user volunteered visual direction during kickoff. Omit if empty.

Format:

```
## Visual Direction

> The following is non-authoritative input for the design agent's foundation pass. The design system owns the actual decisions.

- Feel: <2–3 adjectives>
- Draw from: <reference products, if any>
- Avoid: <what it should not look like>
```

---

## What belongs in the Foundation vs. the coding harness

The foundation captures **everything stable that the user can perceive or rely on**: terminology, voice, platform shape, performance commitments, data-handling commitments, accessibility level, distribution, supported platforms, external integrations the user can see, and the named persistent UI areas. If the user could notice it and it is stable across the product, it belongs here.

The foundation does **not** capture pure implementation choices — module structure, file layout, language tooling, libraries, frameworks, test framework, lint / format, CI/CD, retry numbers, logging format, schema mechanics, monitoring. Those are the coding harness's call. The foundation also does **not** capture buildable work — scenarios and feature specs live in tickets and feature docs.

The boundary is: would the user perceive a difference if this decision were changed? If yes and it is stable across the product → foundation. If yes but it is specific to one feature → a ticket / feature doc. If no → the harness.

---

## Conventions

### EARS acceptance criteria

The foundation itself rarely carries acceptance criteria (it has no scenarios). Where it states a testable commitment, use EARS. The five patterns (also used in tickets and feature docs):

- `WHEN <event/trigger> THE SYSTEM SHALL <observable response>` — event-driven
- `WHILE <state> THE SYSTEM SHALL <ongoing behavior>` — state-driven
- `IF <condition> THEN THE SYSTEM SHALL <response>` — unwanted-behavior / guard
- `WHERE <feature is enabled> THE SYSTEM SHALL <response>` — feature-gated
- `THE SYSTEM SHALL <response>` — ubiquitous (always true)

### Ambiguity markers

`[NEEDS CLARIFICATION: <specific question>]` — place inline wherever a foundation decision is incomplete or deferred. Downstream agents and the build process surface these and should not proceed past unresolved ones. Be specific in the question.

### Cross-references

Refer to terminology and UI elements by their exact name from the Terminology and Experience Overview sections. Do not invent IDs or anchor links — agents resolve references by matching names.

---

## Output skeleton

````markdown
# Foundation

## Project: <name>
_<one-line tagline>_

---

## Product Overview

<Section 1>

## Links

- [Design System](./design-system.md)
- [Interactions](./interactions.md)
- [Feature Index](./features/index.md)
- Backlog: `./backlog/`

## Terminology

<Section 3 — core, cross-cutting concepts only>

## Voice & Tone <!-- omit if no significant user-facing copy -->

<Section 4>

## Platform & Constraints

<Section 5>

## Experience Overview

<Section 6>

## Non-goals

<Section 7>

## Visual Direction <!-- omit if none -->

> Non-authoritative input for the design agent's foundation pass.

<Section 8>
````
