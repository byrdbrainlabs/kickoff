# Feature Doc Schema

This is the canonical structure and convention reference for a **feature doc** — the living, consolidated specification for one capability of the product. Read this whenever you need to write, update, or consume a feature doc — do not invent structure or conventions.

A feature doc is the **current truth** for one capability (e.g., "Resource Ingestion," "Search," "Library"). It is **mutable** and always reflects the product as it stands today. It is the artifact a future agent reads to understand a capability — *not* the ticket history. Tickets are the deltas and the "why"; the feature doc is the consolidated "what is."

A feature doc is also an **architectural boundary.** Its Terminology is the capability's *private* vocabulary — concepts internal to this feature that other features don't need to know. The foundation's Terminology is the *public* vocabulary that features share to reference one another. The documentation agent maintains that boundary.

**Produced and maintained by:** `agents/documentation.md` (folding `done/` tickets in)
**Consumed by:** `agents/feature.md` (context for new tickets), `agents/design.md`, the build process (your coding harness)
**File location:** `docs/features/<capability-slug>.md`, indexed by `docs/features/index.md`

---

## Consolidation, not accumulation

The documentation agent **merges** each done ticket's what + how into the relevant feature doc — it does not append the ticket. When a ticket says "now also support EPUB," the doc changes from "supports PDF" to "supports PDF and EPUB." The result must read as though written fresh today, with no trace of the delta-by-delta history. A reader should never have to reconstruct current state by diffing tickets.

The ticket's **why** (its Motivation) does *not* come into the feature doc — it stays in the immutable `done/` ticket. The feature doc links back to the tickets that shaped it (Section 6) for anyone who needs the rationale.

---

## Frontmatter

```yaml
---
slug: capability-slug
title: Capability Name
---
```

- `slug` matches the filename and the entry in `index.md`.
- `title` is the human-readable capability name (used in Terminology cross-references).

## Sections

A complete feature doc contains the following sections, in order. Optional sections are included only when non-empty.

1. Overview
2. Terminology (optional — feature-local)
3. Behavior
4. Feature Specifications (optional)
5. Design (optional)
6. Related & History

### 1. Overview

1–2 paragraphs: what this capability is, the user value it provides, and where it lives in the experience (referencing persistent UI areas from the foundation's Experience Overview by name). A reader should understand the capability's purpose and entry points before reading the behavior.

**Completion:** A reader can describe what this capability does and where the user encounters it in one or two sentences.

### 2. Terminology (optional — feature-local)

Concepts **internal to this capability** — the feature's private vocabulary. Same entry format as the foundation's Terminology.

Do not duplicate foundation terms here; reference them verbatim. If a term defined here becomes used by other features over time, the documentation agent promotes it to the foundation (and removes it here). Omit this section if the capability introduces no local vocabulary.

### 3. Behavior

The **consolidated current behavior** of the capability — the scenarios and acceptance criteria as they stand today. This is the heart of the doc. Same scenario structure used in tickets and the foundation:

**Context.** The user's role, current state, and goal.
**Steps.** Step-by-step interaction: user action, UI element, system response.
**Acceptance criteria.** EARS-format statements, co-located with the scenario. These are the current **acceptance contract** for this capability — the regression contract future tickets reference and may modify.

Scenarios are self-contained and reference foundation / other-feature concepts by canonical name. Write them as current state, never as "changed from…".

**Completion:** Every current user-facing behavior of the capability appears as a scenario with Context, Steps, and EARS criteria. No behavior is described only in a ticket.

### 4. Feature Specifications (optional)

Detailed specs for behavior too complex to live inline in a scenario — multiple interaction modes, conditional behavior, edge cases. Same structure as in tickets:

- **Core behavior**
- **Conditional behavior**
- **Edge cases and error handling** (trigger, exact feedback shown, recovery path)
- **Acceptance criteria** (EARS, co-located)

Omit if every behavior fits inline in a scenario.

### 5. Design (optional)

The **feature-local** design for this capability — component appearance/states and interaction patterns specific to this feature, consolidated from the tickets that introduced them. References design-system / interactions tokens by name; does not restate global tokens.

Global components and patterns live in `design-system.md` / `interactions.md`, not here. Omit this section if the capability has no feature-local design.

```
### <Component or pattern name>
- **Appearance / states:** … (references design-system tokens)
- **Interaction:** … (references interactions tokens)
```

### 6. Related & History

Two short lists:

- **Related features.** Other capabilities this one interacts with, by canonical name — the cross-feature surface. (This is where the public/shared vocabulary shows up.)
- **History.** Links to the `done/` tickets that created and changed this capability, newest last. Pointers only, not content — this is the rationale trail for "why is it like this?"

```
## Related & History

**Related features.** [Search](./search.md), [Library](./library.md)

**History.**
- `backlog/done/003-resource-ingestion.md` — created the capability (PDF)
- `backlog/done/019-epub-support.md` — added EPUB
```

---

## The feature index (`docs/features/index.md`)

A single manifest the documentation agent maintains and other agents read first to locate the right doc (progressive disclosure — read the index, then the one relevant feature doc, never all of them).

For each capability: slug/title, a one-line summary, and a link. The documentation agent updates this whenever it creates a feature doc or materially changes a capability's summary.

```markdown
# Feature Index

| Capability | Summary | Doc |
|---|---|---|
| Resource Ingestion | Import PDFs/EPUBs into a Library; indexed for search | [resource-ingestion.md](./resource-ingestion.md) |
| Search | Full-text and semantic search across a Library | [search.md](./search.md) |
```

**Completion:** Every file in `docs/features/` has exactly one index row; every index row points to an existing file.

---

## What belongs in a feature doc vs. elsewhere

- **Current behavior, feature-local terminology, feature-local design** → here.
- **Cross-cutting terminology, voice, platform commitments, experience shape** → the foundation.
- **Global components and interaction patterns** → `design-system.md` / `interactions.md`.
- **The "why," and the per-change record** → the immutable `done/` tickets (linked from Section 6).
- **Implementation choices** → the coding harness; never here.

---

## Conventions

### EARS acceptance criteria

The same five patterns as the foundation and tickets:

- `WHEN <event/trigger> THE SYSTEM SHALL <observable response>`
- `WHILE <state> THE SYSTEM SHALL <ongoing behavior>`
- `IF <condition> THEN THE SYSTEM SHALL <response>`
- `WHERE <feature is enabled> THE SYSTEM SHALL <response>`
- `THE SYSTEM SHALL <response>`

### Ambiguity markers

A consolidated feature doc should carry **no** `[NEEDS CLARIFICATION]` markers — they are resolved before a ticket leaves `draft/`. If consolidation surfaces a contradiction between a ticket and the existing doc, the documentation agent stops and flags it rather than guessing.

### Cross-references

Reference UI elements, capabilities, and concepts by their exact canonical name from the foundation or other feature docs. Do not invent IDs or anchor links.

---

## Output skeleton

````markdown
---
slug: <capability-slug>
title: <Capability Name>
---

# Feature: <Capability Name>

## Overview

<Section 1>

## Terminology <!-- omit if none; feature-local only -->

* **<Term>**: <definition>

## Behavior

### Scenario: <name>

**Context.** …
**Steps.** …
**Acceptance criteria.**
- WHEN … THE SYSTEM SHALL …

## Feature Specifications <!-- omit if none -->

### <Feature name>

**Core behavior.** …
**Conditional behavior.** …
**Edge cases and error handling.** …
**Acceptance criteria.**
- WHEN … THE SYSTEM SHALL …

## Design <!-- omit if none; feature-local only -->

### <Component or pattern name>
- **Appearance / states:** …
- **Interaction:** …

## Related & History

**Related features.** …

**History.**
- `backlog/done/<ticket>.md` — <what it did>
````
