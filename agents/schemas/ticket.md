# Ticket Schema

This is the canonical structure and convention reference for a **ticket** — one unit of work in the backlog. A ticket is a new feature, a behavioral change, or a bug fix. Read this whenever you need to write, update, or consume a ticket — do not invent structure or conventions.

A ticket is the **unit of work and the unit of history**. It is created as a stub (by the kickoff agent for the MVP, or by the operator/feature agent later), fleshed out in `backlog/draft/`, built from `backlog/todo/`, and — once in `backlog/done/` — left **immutable** as the historical record of *why* a change was made. Its **what + how** is consolidated into a **feature doc** by the documentation agent; its **why** stays here.

A ticket is **focused** and **self-contained for its delta**. It does not restate the foundation (terminology, platform, voice, experience) — it inherits all of that. It states only what is specific to this change. It may reference foundation concepts and existing feature docs by their canonical names.

**Produced by:** `agents/kickoff.md` (stubs), `agents/feature.md` (fleshed); `agents/design.md` writes the feature-local Design section
**Consumed by:** the build process (your coding harness), `agents/documentation.md`
**File location:** `docs/backlog/{draft,todo,in-progress,done}/<slug>.md` — the folder is the ticket's status

---

## Status is the folder

A ticket has no status field. Its location is its status:

- `backlog/draft/` — being fleshed out (feature + design agents). Stubs start here.
- `backlog/todo/` — fully specified, no open clarifications, ready to build.
- `backlog/in-progress/` — being built.
- `backlog/done/` — built; awaiting or completed documentation consolidation. Immutable.

Moving the file is the state transition; git records it.

## Frontmatter

Every ticket begins with frontmatter:

```yaml
---
slug: ticket-slug
title: Short Title
feature: feature-slug   # optional — the capability this targets, if known
---
```

- `slug` matches the slug portion of the filename.
- `title` is a short human-readable title (≤ 60 characters).
- `feature` (optional) names the feature doc this ticket most likely updates. A hint for the documentation agent — which makes the final new-vs-update call via the feature index.

Filenames carry a leading ordinal prefix (`007-add-epub-support.md`), assigned at creation, unique and ascending (gaps are fine). The prefix is the **default pickup order** for the `todo/` queue — it answers "what's next" and lets the filesystem sort the backlog ascending. It is a soft scheduling hint: the `backlog` agent reports the lowest-numbered `todo/` ticket as next, and the build process may reorder for dependencies it detects from the feature docs.

The ordinal orders the *work queue*, not the *meaning*. It is **not** load-bearing for truth — you never replay tickets in sequence to learn current state; the feature docs and index are the source of truth. So the strict contiguity the old system enforced is gone: numbers must be unique and sortable, nothing more.

## Sections

A complete (fleshed) ticket contains the following sections, in order. Sections marked optional are included only when non-empty. A **stub** (kickoff output) has only Motivation and Scope, with the rest to be filled in during fleshing.

1. Motivation
2. Scope
3. Terminology (optional)
4. Platform & Constraints Amendments (optional)
5. Scenarios
6. Modifications to Existing Behavior (optional)
7. Feature Specifications (optional)
8. Design (optional)

### 1. Motivation

A short (1–3 paragraphs) statement of *why* this change exists. Problem or opportunity, the user signal that prompted it, expected impact. This is the "why" — and it is the part that **stays in the ticket as history** after the rest is consolidated into the feature doc.

**Completion:** A reader unfamiliar with this change can describe in one sentence why it's being made.

### 2. Scope

A one-paragraph summary of what this ticket adds or changes. State new behaviors and surfaces; explicitly note anything notable that's intentionally out of scope for this ticket.

**Completion:** A reader can predict which scenarios and features will appear below from reading the scope alone.

### 3. Terminology (optional)

New concepts introduced by this ticket. Same format as the foundation's Terminology section. Use these names verbatim everywhere else in the ticket.

By default, terms introduced here are **feature-local** — they will land in the relevant feature doc at consolidation, not the foundation. If a term is genuinely cross-cutting, say so; the documentation agent decides whether to promote it into the foundation (a deliberate amendment).

If this ticket renames or deprecates an existing concept (from the foundation or a feature doc), state the rename explicitly with old and new names — do not silently shadow existing vocabulary.

Include this section only if new concepts are introduced. Otherwise omit.

### 4. Platform & Constraints Amendments (optional)

Explicit changes to commitments in the foundation's Platform & Constraints. Each amendment names the item it changes and states the new commitment.

Usually empty. Include only if a commitment genuinely changes (e.g., a sync feature changes "all data stays on the device" to "data syncs to a server when accounts are enabled").

If an amendment alters a commitment that shipped code or other features depend on, flag it explicitly to the user before writing.

### 5. Scenarios

The user scenarios this ticket introduces or changes. Same structure as a feature doc's scenarios:

**Context.** The user's role, current state, and specific goal.

**Steps.** Step-by-step interaction. Each step: the user's action, the UI element interacted with, the system's response.

**Acceptance criteria.** 2–6 EARS-format statements, co-located with the scenario.

**Features referenced.** Links to feature specs (Section 7) where applicable.

Each scenario is self-contained and may reference UI elements and concepts from the foundation or existing feature docs by their canonical name.

#### DOs
- DO specify every UI element a user interacts with, and every element that gives context or feedback.
- DO specify conditional UI elements and alternate paths.
- DO use the names defined in the foundation's Experience Overview and Terminology.
- DO use `[NEEDS CLARIFICATION: <specific question>]` inline for ambiguities.

#### DON'Ts
- DO NOT inline a feature spec longer than ~6 lines — move it to Section 7.
- DO NOT pre-decompose into engineering tasks, file structures, or build phases.
- DO NOT include implementation details. Describe only what the user perceives.

**Completion:** Every scenario has Context, Steps, and at least 2 EARS criteria. Apply the kickoff agent's Semantic completeness checks during gathering — a scenario is not finished while temporal, identity, lifecycle, or boundary semantics for the concepts it touches remain implicit.

### 6. Modifications to Existing Behavior (optional)

Explicit changes to behavior defined in the foundation or an existing feature doc. Each modification names the source by canonical name and states the change concretely.

Format:

```
### Modifies: <Scenario or Feature canonical name>

**Source.** [Feature doc: resource-ingestion] or [Foundation, Experience Overview]
**Change.** A one-paragraph description of what behavior is now different.
**Updated acceptance criteria.**
- WHEN … THE SYSTEM SHALL …
```

Modifications should be deliberate. Include this section only if modifications exist. Otherwise omit.

### 7. Feature Specifications (optional)

Detailed specs for behavior too complex to live inline in a scenario (multiple interaction modes, conditional behaviors, or more than ~6 lines).

Each feature spec has:

- **Core behavior.** The primary functionality and happy-path interactions.
- **Conditional behavior.** Per user role / state / configuration, what differs.
- **Edge cases and error handling.** Each error state or edge case, what triggers it, the exact feedback shown, how the user recovers.
- **Acceptance criteria.** EARS-format, co-located with the behavior they describe.

Include this section only if at least one new behavior needs a full spec. Otherwise omit.

### 8. Design (optional)

The **feature-local** design decisions for this ticket, written by the design agent during fleshing. These are resolved decisions (not concerns) specific to this feature's UI — component appearance/states and interaction patterns that are not global enough to live in `design-system.md` / `interactions.md`.

Global design decisions this ticket surfaced go **directly into** the design system / interactions documents (the design agent writes them there immediately), **not** here. Only the feature-local remainder lives in this section. At consolidation, this section lands in the feature doc.

Include this section only if the ticket has feature-local design. Otherwise omit.

```
## Design

### <Component or pattern name> <!-- feature-local -->
- **Appearance / states:** … (references design-system tokens by name)
- **Interaction:** … (references interactions tokens by name)
```

---

## What belongs in a ticket vs. elsewhere

- **Pure implementation details** the user volunteers (test framework, libraries, file layout, retry tuning, CI) — the coding harness decides. Acknowledge and do not capture.
- **User-facing change to a stable commitment** — a Platform & Constraints Amendment (Section 4), which amends the foundation.
- **A new global component or interaction pattern** — the design agent writes it directly into `design-system.md` / `interactions.md`; only feature-local design stays in Section 8.
- **Adjacent scope the user raises** — recommend a separate ticket. Do not bloat this one.

---

## Conventions

### Ubiquitous language

Use foundation Terminology verbatim. Add new terms under Section 3 (feature-local by default). Do not silently introduce a synonym for an existing concept — surface the overlap and ask reuse / rename / deprecate.

### EARS acceptance criteria

The same five patterns as the foundation and feature docs:

- `WHEN <event/trigger> THE SYSTEM SHALL <observable response>`
- `WHILE <state> THE SYSTEM SHALL <ongoing behavior>`
- `IF <condition> THEN THE SYSTEM SHALL <response>`
- `WHERE <feature is enabled> THE SYSTEM SHALL <response>`
- `THE SYSTEM SHALL <response>`

### Ambiguity markers

`[NEEDS CLARIFICATION: <specific question>]` — same convention as the foundation. A ticket may not move from `draft/` to `todo/` while unresolved markers remain, and the build process should not proceed past them.

### Cross-references

Reference UI elements, scenarios, and features by their exact canonical name from the foundation or existing feature docs. Do not invent IDs or anchor links.

---

## Output skeleton

````markdown
---
slug: <ticket-slug>
title: <Short Title>
feature: <feature-slug>   # optional
---

# Ticket: <Short Title>

## Motivation

<Section 1 — the "why"; stays as history>

## Scope

<Section 2>

## Terminology <!-- omit if none; feature-local by default -->

* **<Term>**: <definition>

## Platform & Constraints Amendments <!-- omit if none -->

- **<item being amended>:** <new commitment>

## Scenarios

### Scenario X: <name>

**Context.** …
**Steps.** …
**Acceptance criteria.**
- WHEN … THE SYSTEM SHALL …
**Features referenced.** [Feature Y](#feature-y) <!-- if any -->

## Modifications to Existing Behavior <!-- omit if none -->

### Modifies: <canonical name>

**Source.** …
**Change.** …
**Updated acceptance criteria.**
- WHEN … THE SYSTEM SHALL …

## Feature Specifications <!-- omit if none -->

### Feature Y: <name>

**Core behavior.** …
**Conditional behavior.** …
**Edge cases and error handling.** …
**Acceptance criteria.**
- WHEN … THE SYSTEM SHALL …

## Design <!-- omit if none; feature-local only -->

### <Component or pattern name>
- **Appearance / states:** …
- **Interaction:** …
````
