# Semantic Completeness

This is the canonical reference for **semantic completeness** — the checks that surface the implicit time, identity, lifecycle, and boundary behavior that determine *exactly* what the product does at the edges. Read this whenever you need to pressure-test a concept — do not invent an alternative checklist.

**Applied by:** `agents/kickoff.md` (lightly — only enough to define each core Terminology concept coherently) and `agents/feature.md` (in full, for every new concept a ticket introduces — this is where the heavy semantic work lives).

---

## How to apply

For each concept, work through the question categories below. Each answer has a destination:

- A new **concept name** → Terminology, referenced verbatim.
- An **observable behavior** → a scenario or feature spec (at kickoff: note it for the relevant stub; the feature agent will detail it), with an EARS criterion if testable.
- A **deliberate omission** → Non-goals (stated positively).
- An **answer you cannot extract** → `[NEEDS CLARIFICATION: <specific question>]` inline.

Skip a category when clearly inapplicable (e.g., temporal questions for a stateless calculator).

## Temporal

- When does a time-bounded concept ("today," "this session," "this week") change for the user? Device-local midnight? UTC? A custom rollover?
- How does it behave across timezone changes, DST, or device-clock changes?
- If a time boundary is crossed mid-action, what happens?

## Identity

- When are two of these "the same"? Case sensitivity, whitespace, accent folding, punctuation.
- If the user creates one, deletes it, and creates another with the same name, are they the same thing? (Identity by name, or by an opaque token the user never sees?)

## Lifecycle

- What events create this concept? What changes it? What destroys it?
- What happens to dependent state when it's destroyed?
- Which transitions are reversible? Which require explicit confirmation?

## State transitions (for concepts with state)

- What states can it be in? Name each and add it to Terminology.
- What transitions are valid, and in which directions?
- Which transitions are automatic (time/event-based) vs. user-initiated?

## Boundary behavior

- What happens at connectivity transitions (online → offline → online)?
- What happens at device-state transitions (background → foreground, sleep → wake)?
- What happens during simultaneous or rapid-repeat actions on the same target?
