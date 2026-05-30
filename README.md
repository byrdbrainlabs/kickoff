# Kickoff

A harness-agnostic system of requirements agents that produce the artifacts a coding agent needs to build a software product autonomously — and to keep extending it, one ticket at a time.

It is built on one idea: **separate what the product *is* from the work that *changes* it.** Stable, product-wide context lives in a foundation that every agent inherits. Buildable work flows through a backlog of tickets. What's been built is consolidated into living feature docs that stay current. Implementation — language, framework, file layout, architecture — is left entirely to the coding harness, which is the open experimentation surface.

## Getting started

Clone this repo (or work in it directly), then tell your coding assistant to **read `agents/kickoff.md` and follow it**. Each agent file is a complete, self-contained instruction set; it pulls in the schema(s) it needs from `agents/schemas/`. To run *any* agent, point your assistant at the matching `agents/<name>.md`.

A typical run: **kickoff** (foundation + MVP ticket stubs) → **design** foundation pass (design system + interactions) → then, per ticket, **feature** (+ **design** as needed) specs it into `todo/`, your build process builds it, **backlog** moves it to `done/`, and **documentation** folds it into the feature docs. See [Lifecycle](#lifecycle) for the full loop.

## The three tiers

| Tier | Artifacts | Role |
|---|---|---|
| **Foundation** | `docs/foundation.md`, `docs/design-system.md`, `docs/interactions.md` | Stable, product-wide context. Terminology, voice, platform commitments, experience shape, non-goals; the global design language. Inherited by every agent; amended rarely and deliberately. |
| **Feature docs** | `docs/features/<capability>.md` + `docs/features/index.md` | The living, consolidated truth of each capability — always current. What a future agent reads to understand the product. |
| **Backlog** | `docs/backlog/{draft,todo,in-progress,done}/<ticket>.md` | Units of work — a feature, a behavior change, a bug fix. Append-only history; immutable once `done/`. |

A **ticket** is the unit of work; building it updates a **feature doc**; both inherit the **foundation**. When a ticket finishes, its *what + how* is consolidated into the feature doc (current truth) and its *why* stays in the `done/` ticket (history) — so nobody has to replay tickets to understand where the product stands today.

## The five agents

| Agent | Reads | Produces |
|---|---|---|
| `agents/kickoff.md` | (nothing) | `docs/foundation.md` + stubbed MVP tickets in `docs/backlog/draft/` |
| `agents/design.md` | foundation, feature docs, the ticket | `docs/design-system.md` & `docs/interactions.md` (global) + feature-local design into the ticket |
| `agents/feature.md` | foundation, feature index + docs, the ticket | a fully-specified ticket, moved `draft/ → todo/` |
| `agents/backlog.md` | the backlog folders | ticket moves (`next` / `start` / `done` / `ready`); the hook where documentation kicks in |
| `agents/documentation.md` | the done ticket, feature index + docs | updated/created feature docs + index |

There is no build agent — **how you turn `todo/` tickets into code is yours** (see [Building](#building)). Each requirements output has a schema under `agents/schemas/` defining its structure, completion criteria, conventions, and sensible defaults; the backlog agent has no schema (it only moves files).

## Lifecycle

**Kickoff** — run once at product inception.

```
kickoff   ──►  foundation.md  +  stubbed tickets in backlog/draft/
                     │
                     ▼
design (foundation pass)  ──►  design-system.md + interactions.md
```

The kickoff agent captures everything stable and *draws the MVP line* — scoping the initial features into stub tickets without detailing them. The design agent's foundation pass then establishes the global design language (mostly from sensible defaults). Neither goes deep on individual features.

**Per ticket** — the loop that runs for every feature or change, MVP and beyond.

```
feature  ──►  fleshes a draft ticket (behavior, EARS criteria, semantics)
   │            │  surfaces design needs ──► design (per-ticket):
   │            │                              global → design-system / interactions
   │            │                              local  → into the ticket
   ▼            ▼
ticket moves draft/ ──► todo/
                          │
   your build process  ──►  builds the next ticket; the backlog agent moves it
                          │   todo/ → in-progress/ → done/
                          ▼
        documentation  ──►  folds the done ticket into features/<capability>.md,
                            updates the index, leaves the ticket as history
```

New work is just a new ticket through the same loop. The feature agent reads the feature index first, so it reuses and references existing capabilities instead of rediscovering them.

## Folders are inboxes

A ticket has no status field — its **folder is its status**, and moving the file is the state transition (tracked by git). Each folder is the inbox for one agent:

- `backlog/draft/` → the **feature** + **design** agents (being specified)
- `backlog/todo/` → ready to build; the **backlog** agent hands the next ticket (lowest ordinal) to your build process
- `backlog/in-progress/` → being built
- `backlog/done/` → the **documentation** agent (built; then terminal history)

The **backlog** agent is the one component that knows this folder protocol — so your build process never has to. It talks to the backlog agent (`next`, `start`, `done`) and stays a plain "implement this ticket" coding harness.

Handoffs are **soft**: a finishing agent recommends what to run next, the folder carries the state, and the operator (or harness) invokes the next agent. There is no agent-calls-agent machinery.

## Building

Turning `todo/` tickets into working code is intentionally **outside** this system — it's the open experimentation surface. Point Claude Code at the repo and say "build the next ticket," wire up an orchestrator, or do it by hand. Whatever you use, the contract is small:

- **Read** the foundation (`foundation.md`, `design-system.md`, `interactions.md`) and the relevant feature docs for context; build the ticket the **backlog** agent hands you (`next` / `start`).
- **Done means:** every EARS acceptance criterion in the ticket is satisfied and verifiable, every Platform & Constraints commitment in the foundation is respected, and no `[NEEDS CLARIFICATION]` markers remain. If the project doesn't exist yet, scaffolding it (from the foundation's application shape) is just part of building the first ticket.
- **Decide freely:** language, framework, libraries, file layout, tests, architecture — anything the user can't perceive.
- **Escalate, don't invent:** if you'd have to make a product-visible decision the artifacts didn't make, stop and route it to the `kickoff` or `feature` agent. If a build-time discovery changes a ticket's scope, fold that change back into the ticket before it reaches `done/`.
- **Close out:** tell the backlog agent you're `done`; it moves the ticket to `done/` and points to the `documentation` agent to consolidate.

## Harness-agnostic

These agents are prose specifications. They do **not** assume any particular coding harness, runtime, or invocation model. The simplest way to use Kickstart is to **clone this repo and work inside it** — the agents write into `docs/` and your product is built in the same directory. (You can equally lift the `agents/` files into another harness as system prompts, sub-agents, or slash commands.)

This repo deliberately does **not** prescribe:

- Which coding harness builds the tickets (Claude Code, Cursor, Aider, Codex, your own)
- How agents are invoked or how handoff happens (chat, CLI, scripted, automated)
- The build's implementation choices — language, framework, libraries, file layout, test framework, CI, **or software architecture**

That last point is deliberate. There is **no implementation-plan step and no architecture document** in this system — both would pull implementation decisions back across the line and couple the system to one way of getting from requirements to working software. Keeping that step open is the primary thing a consumer of Kickstart is meant to experiment with.

The dividing line is **"would the user perceive a difference if this decision were changed?"** If yes and it's stable across the product → foundation. If yes but specific to one feature → a ticket / feature doc. If no → the coding harness decides.

## Conventions

- **EARS acceptance criteria** — every behavior gets a testable statement (`WHEN … THE SYSTEM SHALL …`).
- **`[NEEDS CLARIFICATION: <question>]`** — visible blockers. A ticket can't leave `draft/`, and the build shouldn't proceed, while any remain.
- **`<!-- default -->`** — tags every design value an agent chose vs. received from the user, so a reviewer sees at a glance what to check first.
- **Ubiquitous language, split global vs. local** — the foundation's Terminology is the shared, cross-cutting vocabulary features use to reference each other; a feature doc's Terminology is that capability's private vocabulary. The documentation agent maintains the boundary and promotes a term to the foundation only when it becomes genuinely cross-cutting.
- **Consolidation, not accumulation** — feature docs are rewritten to current truth as tickets land, never a pile of deltas. Tickets are the history; feature docs are the present.
- **Each agent knows its own done-ness** — there is no separate verification/linter agent. Verification and process belong to the operator and harness.

## Layout

```
agents/
  kickoff.md
  feature.md
  design.md
  backlog.md
  documentation.md
  schemas/
    foundation.md
    ticket.md
    feature-doc.md
    design-system.md
    interactions.md

docs/                       # ships as an empty scaffold; the agents fill it in
  foundation.md             # placeholder until the kickoff agent runs
  design-system.md          # placeholder until the design agent runs
  interactions.md           # placeholder until the design agent runs
  features/
    index.md                # placeholder until the documentation agent runs
    <capability>.md         # created as features ship
  backlog/
    draft/  todo/  in-progress/  done/    # tickets live here; folder = status
```

The `docs/` tree ships pre-created so the shape is visible on clone. The foundation files start as placeholders (an HTML comment); agents treat a placeholder as "not yet generated" and populate it on first run.

## Status

The schemas and agents are the stable core. There is deliberately no build agent — how you turn tickets into code (and what architecture it chooses) is the primary open experimentation surface. There is no bundled worked example yet; the cleanest way to see the system is to clone the repo and run the kickoff agent on a small idea, then follow the loop.

## License

See [LICENSE](./LICENSE).
