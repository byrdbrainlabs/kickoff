# Ticket Schema

This is the canonical structure and convention reference for a **ticket** — one unit of work in the backlog. A ticket is a new feature, a behavioral change, or a bug fix. Read this whenever you need to write, update, or consume a ticket — do not invent structure or conventions.

A ticket is the **unit of work and the unit of history**. It is created as a stub (by the kickoff agent for the MVP, or by the operator/feature agent later), fleshed out in `backlog/draft/`, built from `backlog/todo/`, and — once in `backlog/done/` — left **immutable** as the historical record of *why* a change was made. Its **what + how** is consolidated into a **feature doc** by the documentation agent; its **why** stays here.

A ticket is **focused** and **self-contained for its delta**. It does not restate the foundation (terminology, platform, voice, experience) — it inherits all of that. It states only what is specific to this change. It may reference foundation concepts, existing feature docs, and lower-numbered tickets by their canonical names.

**Produced by:** `agents/kickoff.md` (stubs), `agents/feature.md` (fleshed); `agents/design.md` writes the feature-local Design section; the build process appends Build Notes at close-out
**Consumed by:** the build process (your coding harness), `agents/documentation.md`
**File location:** `docs/backlog/{draft,todo,in-progress,done}/NNN-<slug>.md` — the folder is the ticket's status

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
feature: feature-slug            # optional — the capability this targets, if known
introduces: [Term A, Term B]     # canonical terms this ticket defines (fleshed tickets; omit if none)
modifies: [Term C]               # canonical names whose behavior this ticket changes (omit if none)
supersedes: [AC-004.2]           # criterion IDs this ticket retires (omit if none)
depends_on: [001-app-shell]      # lower-numbered tickets this one requires built ([] if independent)
---
```

- `slug` matches the slug portion of the filename.
- `title` is a short human-readable title (≤ 60 characters).
- `feature` (optional) names the feature doc this ticket most likely updates. A hint for the documentation agent — which makes the final new-vs-update call via the feature index.
- `introduces` / `modifies` are the ticket's **exports**, maintained by the feature agent during fleshing (stubs don't have them yet). They exist so agents skimming the backlog can answer "which ticket defines the concept I'm referencing?" and "which tickets change this behavior?" from frontmatter alone, without reading ticket bodies. **Sync rule:** every entry in the ticket's Terminology section appears in `introduces`, and every `Modifies:` target in Section 6 appears in `modifies` — no more, no less. One carve-out: a Terminology entry that **annotates an existing foundation or feature-doc term** (adding feature context to a concept defined elsewhere, and saying so) is excluded from `introduces` — that list answers "which ticket *defines* this concept?", and for an annotated term the answer is the original definition's home. Omit either field when empty.
- `supersedes` lists the **criterion IDs** (see Conventions → Criterion IDs) this ticket retires — statements that stop being true of the product once this ticket is built. **Sync rule:** it is exactly the union of the `Supersedes.` lines in Section 6 — no more, no less. Every retired ID belongs to a ticket in `depends_on` (you can only retire behavior you build on). A retired ID is usually replaced by this ticket's own updated criteria, but may stand unreplaced when a behavior is removed outright. This field is what makes the **active acceptance contract** computable from frontmatter alone: at any point in the backlog, the criteria in force are all criteria minus the superseded IDs — the contract any build of the backlog must satisfy. Omit when empty.
- `depends_on` lists the lower-numbered tickets (by file stem, `NNN-slug`) that must be **built** before this one can run — direct dependencies only; schedulers compute the closure. The feature agent derives it during fleshing: any lower-numbered ticket whose concepts this ticket references, whose behavior it modifies, or whose Platform & Constraints Amendment it relies on is a dependency. A fleshed ticket always carries the field; `[]` means independent — buildable any time. Stubs don't have it yet.

A `todo/` ticket is **ready** when every ticket in its `depends_on` is in `done/`. Tickets with disjoint dependency closures may build **in parallel** — `depends_on` captures semantic dependence; code-level conflicts between parallel builds are the build harness's concern.

Frontmatter + Scope together are the ticket's **skim layer**: agents fleshing higher-numbered tickets read these for every lower-numbered ticket and pull full bodies only where the named concepts overlap.

Filenames carry a leading ordinal prefix (`007-add-epub-support.md`), assigned at creation, unique and ascending (gaps are fine). The prefix is the **specification order contract**: tickets are fleshed in ascending order and may reference only lower-numbered tickets — which keeps the `depends_on` graph acyclic by construction. At build time, `depends_on` governs: a ticket runs once its dependencies are `done/` and may assume only its dependency closure is built. Among ready tickets, the ordinal is the pickup priority — the `backlog` agent reports the lowest-numbered ready ticket first.

The ordinal orders the *work queue*, not the *meaning* — you never replay tickets in sequence to learn current built state; the feature docs (run state) consolidate that. Strict contiguity is not required: numbers must be unique and ascending, nothing more.

## Sections

A complete (fleshed) ticket contains the following sections, in order. Sections marked optional are included only when non-empty. A **stub** (kickoff output) has only Motivation and Scope, with the rest to be filled in during fleshing. Build Notes is not part of fleshing — the build process appends it at close-out.

1. Motivation
2. Scope
3. Terminology (optional)
4. Platform & Constraints Amendments (optional)
5. Scenarios
6. Modifications to Existing Behavior (optional)
7. Feature Specifications (optional)
8. Design (optional)
9. Build Notes (build process only)

### 1. Motivation

A short (1–3 paragraphs) statement of *why* this change exists. Problem or opportunity, the user signal that prompted it, expected impact. This is the "why" — and it is the part that **stays in the ticket as history** after the rest is consolidated into the feature doc.

**Completion:** A reader unfamiliar with this change can describe in one sentence why it's being made.

### 2. Scope

A one-paragraph summary of what this ticket adds or changes. State new behaviors and surfaces; explicitly note anything notable that's intentionally out of scope for this ticket.

Scope doubles as this ticket's **index entry**: agents fleshing higher-numbered tickets skim frontmatter + Scope to decide whether to read the full ticket. Write it to support that decision.

**Completion:** A reader can predict which scenarios and features will appear below from reading the scope alone, and can decide from Scope alone whether this ticket is relevant to another.

### 3. Terminology (optional)

New concepts introduced by this ticket. Same format as the foundation's Terminology section. Use these names verbatim everywhere else in the ticket.

By default, terms introduced here are **feature-local** — they will land in the relevant feature doc at consolidation, not the foundation. If a term is genuinely cross-cutting, say so; the documentation agent decides whether to promote it into the foundation (a deliberate amendment).

If this ticket renames or deprecates an existing concept (from the foundation or a feature doc), state the rename explicitly with old and new names — do not silently shadow existing vocabulary.

Keep the frontmatter `introduces` list in sync: every term **defined** here appears there, by exact name. An entry that merely annotates an existing foundation or feature-doc term (mark it as such, e.g. "(Foundation term; this ticket realizes it for …)") stays out of `introduces` — the concept's definition lives elsewhere.

Include this section only if new concepts are introduced. Otherwise omit.

### 4. Platform & Constraints Amendments (optional)

Explicit changes to commitments in the foundation's Platform & Constraints. Each amendment names the item it changes and states the new commitment.

Usually empty. Include only if a commitment genuinely changes (e.g., a sync feature changes "all data stays on the device" to "data syncs to a server when accounts are enabled").

During the build of this ticket, an amendment **supersedes** the foundation's version of the commitment it changes — the build follows the amendment, not the (not-yet-updated) foundation. The documentation agent applies the amendment to the foundation at consolidation, after the ticket is done.

If an amendment alters a commitment that shipped code or other features depend on, flag it explicitly to the user before writing.

### 5. Scenarios

The user scenarios this ticket introduces or changes. Same structure as a feature doc's scenarios:

**Context.** The user's role, current state, and specific goal.

**Steps.** Step-by-step interaction. Each step: the user's action, the UI element interacted with, the system's response.

**Acceptance criteria.** 2–6 EARS-format statements, co-located with the scenario, each carrying its criterion ID (see Conventions → Criterion IDs).

**Features referenced.** Links to feature specs (Section 7) where applicable.

Each scenario is self-contained and may reference UI elements and concepts from the foundation or existing feature docs by their canonical name.

#### DOs
- DO specify every UI element a user interacts with, and every element that gives context or feedback.
- DO specify conditional UI elements and alternate paths.
- DO use the names defined in the foundation's Experience Overview and Terminology — every element named becomes a touch point (see Conventions).
- DO use `[NEEDS CLARIFICATION: <specific question>]` inline for ambiguities.

#### DON'Ts
- DO NOT inline a feature spec longer than ~6 lines — move it to Section 7.
- DO NOT pre-decompose into engineering tasks, file structures, or build phases.
- DO NOT include implementation details. Describe only what the user perceives.

**Completion:** Every scenario has Context, Steps, and at least 2 EARS criteria. Apply the Semantic completeness checks (`agents/schemas/semantic-completeness.md`) during gathering — a scenario is not finished while temporal, identity, lifecycle, or boundary semantics for the concepts it touches remain implicit.

### 6. Modifications to Existing Behavior (optional)

Explicit changes to behavior defined in the foundation, an existing feature doc, or — when specifying ahead of the build — a lower-numbered ticket's planned behavior. Each modification names the source by canonical name and states the change concretely.

Format:

```
### Modifies: <Scenario or Feature canonical name>

**Source.** [Feature doc: resource-ingestion], [Foundation, Experience Overview], or [Ticket: 015-full-text-search]
**Change.** A one-paragraph description of what behavior is now different.
**Supersedes.** `AC-015.2`, `AC-015.3` — the criterion IDs no longer true as stated. Omit the line if the change retires no existing criteria (e.g., it modifies foundation behavior that carries no IDs).
**Updated acceptance criteria.**
- `AC-021.4` WHEN … THE SYSTEM SHALL …
```

Modifications should be deliberate. Keep the frontmatter `modifies` list in sync: every `Modifies:` target here appears there, by exact canonical name. Likewise `supersedes`: the frontmatter list is exactly the union of the `Supersedes.` lines here. A criterion may be retired without replacement (a behavior removed outright) — the `Supersedes.` line then stands without updated criteria.

Include this section only if modifications exist. Otherwise omit.

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

### 9. Build Notes (build process only)

Appended by the **build process** as part of closing the ticket out, before the `backlog` agent moves it to `done/`. A short record of what actually happened wherever the build deviated from or refined the spec: discoveries made, ambiguities resolved in an obvious direction, behavior implemented differently than written (and why).

**Observations only — no product decisions.** If the build would have to make a product-visible choice the spec didn't make, it does not write a note; it **blocks** (records the question as `[NEEDS CLARIFICATION]` markers and hands the ticket to the `backlog` agent to move back to `draft/`).

This is the one section of a ticket the build process writes; the spec agents never touch it. The `documentation` agent consolidates built truth from the spec **plus** these notes — a deviation recorded here is part of what was actually built. Once the ticket is in `done/`, the notes are immutable with the rest of the ticket.

Omit if the build matched the spec exactly.

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

### Touch points

Every UI element, command, or operation a scenario or feature spec names is a **touch point**: a programmatic testing target. No extra markup is needed — naming it in ubiquitous language *is* the specification, because the foundation's Semantic addressability commitment guarantees the built element is locatable by that canonical name through the platform's semantic surface (role + accessible name for UIs; subcommand/flag for CLIs; resource/operation for APIs).

This is deliberately as far as a ticket goes toward testing. No selectors, no test IDs, no test plans, and no constraint on how a build tests itself — the build's own test suite is an implementation detail entirely outside this system. Touch points exist, together with the foundation's Launch contract and Initial state commitments, so that a conformance suite can be written later, straight from the tickets, against any conforming build — without ever having been part of one.

### Criterion IDs

Every EARS criterion in a ticket carries a stable ID — `AC-<ordinal>.<n>`, the ticket's filename ordinal plus a per-ticket counter — written as a code span at the start of the line:

```
- `AC-012.3` WHEN … THE SYSTEM SHALL …
```

The feature agent assigns IDs in order of appearance while fleshing. IDs are **immutable once assigned**: never renumbered, never reused. If a criterion is deleted during drafting, its number retires with it — gaps are fine, mirroring ticket ordinals. On a re-flesh after a block, unchanged criteria keep their IDs and new criteria take the next unused numbers.

IDs are what make criteria addressable across the system: a later ticket retires an ID by naming it in a `Supersedes.` line (Section 6) and its `supersedes` frontmatter, and feature docs carry IDs through consolidation — so the **active acceptance contract** (all criteria minus superseded ones) stays computable and traceable to its origin, ready for a build-independent test suite to target whenever the operator chooses to write one.

The foundation's commitments carry no IDs — where a foundation commitment is testable, the tickets that realize it state it as their own criteria.

### Ambiguity markers

`[NEEDS CLARIFICATION: <specific question>]` — same convention as the foundation. A ticket may not move from `draft/` to `todo/` while unresolved markers remain, and the build process should not proceed past them.

### Cross-references

Reference UI elements, scenarios, and features by their exact canonical name from the foundation, existing feature docs, or a lower-numbered ticket. Do not invent IDs or anchor links.

---

## Output skeleton

````markdown
---
slug: <ticket-slug>
title: <Short Title>
feature: <feature-slug>          # optional
introduces: [<Term>, …]          # omit if none
modifies: [<canonical name>, …]  # omit if none
supersedes: [<AC-NNN.k>, …]      # omit if none
depends_on: [<NNN-slug>, …]      # [] if independent
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
- `AC-NNN.k` WHEN … THE SYSTEM SHALL …
**Features referenced.** [Feature Y](#feature-y) <!-- if any -->

## Modifications to Existing Behavior <!-- omit if none -->

### Modifies: <canonical name>

**Source.** …
**Change.** …
**Supersedes.** `AC-NNN.k`, … <!-- omit if none retired -->
**Updated acceptance criteria.**
- `AC-NNN.k` WHEN … THE SYSTEM SHALL …

## Feature Specifications <!-- omit if none -->

### Feature Y: <name>

**Core behavior.** …
**Conditional behavior.** …
**Edge cases and error handling.** …
**Acceptance criteria.**
- `AC-NNN.k` WHEN … THE SYSTEM SHALL …

## Design <!-- omit if none; feature-local only -->

### <Component or pattern name>
- **Appearance / states:** …
- **Interaction:** …

## Build Notes <!-- build process only; appended at close-out; omit if build matched spec -->

- <deviation or discovery, and why>
````
