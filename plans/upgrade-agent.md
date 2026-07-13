# Plan: Upgrade Agent

Give Kickoff a permanent upgrade path: when the framework's prompts and schemas change, a consumer project (a repo cloned from Kickoff, with its own product docs and backlog) can be brought up to date — framework files synced, existing artifacts migrated to the new schemas — without hand-work and without clobbering anything the consumer owns.

**Status:** planned, not built. Informed by the one real migration done so far (the `notebook` project: old schema → exports / `depends_on` / Build Notes era), which was performed with a hand-written one-off agent and then verified.

---

## Design decisions (settled)

### 1. The upgrader lives in the framework repo and always executes from the NEW version

The stale-upgrader paradox: an upgrade agent copied into a consumer project at clone time is *old* precisely when it's needed — it cannot know schemas that didn't exist when it was written. So:

- The real agent is `agents/upgrade.md` **in this repo**, maintained alongside the framework.
- What ships in a consumer project is a **thin pointer** (same philosophy as the install agent's wrappers): fetch the current Kickoff framework and follow **its** `agents/upgrade.md` against this project. The instructions always come from the new version.

### 2. One agent, two invocation directions

The agent takes a **source** (the new framework: a local Kickoff clone or the GitHub repo — clone/fetch to a scratch location if given a URL) and a **target** (the consumer project). It asks for whichever it wasn't given. Consumers will normally run the pointer from inside their project; running from a Kickoff clone pointed at a target directory is the same operation.

### 3. Version detection: history match now, stamp going forward

- **History match:** compare the target's `agents/**` files against the source repo's git history to find the commit they match. This is how the notebook migration established its base (byte-identical to the initial commit).
- **Drift detection for free:** if the target's framework files match *no* historical version, someone hand-edited them. The agent stops and surfaces the drift (file-by-file) before any overwrite. Never silently clobber local framework edits.
- **Version stamp:** introduce `agents/VERSION` (the framework commit hash or a simple counter, written by this repo and updated by the upgrade agent). Makes future detection instant; history match remains the fallback for pre-stamp consumers.

### 4. Migrations are recorded, not derived

The agent must not re-derive artifact migrations by diffing schemas and reasoning at upgrade time — too much judgment at the riskiest moment. Instead, the database-migration pattern:

- This repo maintains **`MIGRATIONS.md`** (or `migrations/NNN-<slug>.md` if entries get long).
- **Process rule for this repo:** any change that affects artifacts consumers have already authored (ticket schema, foundation schema, feature-doc schema, conventions) must append a migration entry in the same commit. Prompt-only changes (agent behavior, wording) need no entry — framework sync covers them.
- At upgrade time the agent replays the entries between the target's base version and HEAD, **in order**.

### 5. Framework sync is scoped to `agents/**`

- `agents/**` is framework-owned: overwrite (after the drift check).
- `docs/**` is consumer-owned: never touched by sync; only migrations touch it, per their recipes.
- **README** is ambiguous: a spec-only repo wants the framework README; a product built in-place will eventually have its own. The agent checks whether the target README matches a historical framework README — if yes, sync it; if no, leave it and note it.
- Consumer-created files the framework doesn't know about (e.g., a one-off agent) are left alone and listed in the report.

### 6. Operator checkpoints are part of each migration recipe

Learned from the notebook run: the recipes themselves encode where human confirmation is required (e.g., confirm each derived `depends_on` list; restate — never invent — acceptance criteria; the annotation carve-out for `introduces`). The upgrade agent's own protocol stays generic; judgment lives in the entries.

---

## Components to build

| # | Component | Where | Notes |
|---|---|---|---|
| 1 | `agents/upgrade.md` | this repo | The real agent (protocol below) |
| 2 | `MIGRATIONS.md` | this repo | Seeded with entry 001 (see below) |
| 3 | `agents/VERSION` | this repo | Stamp; updated on framework changes and written into targets on upgrade |
| 4 | Upgrade pointer | consumer projects | Thin pointer file; **created by the install agent** alongside its other wrappers (install agent gains one row) |
| 5 | README updates | this repo | Upgrade path documented in Getting started + agents list + layout |

### The upgrade protocol (sketch for `agents/upgrade.md`)

1. **Resolve source and target.** Ask for whichever is missing. If source is a URL, clone to a scratch directory.
2. **Precondition:** target is a git repo with a clean tree (the whole upgrade must land as one reviewable diff). If not clean, stop.
3. **Detect base version:** `agents/VERSION` if present, else history match against source. If neither resolves → framework files were modified → report the drift file-by-file and stop for operator direction.
4. **Sync framework:** overwrite target `agents/**` from source; README per the ownership check; write the new `VERSION`.
5. **Replay migrations:** apply each `MIGRATIONS.md` entry between base and HEAD in order, honoring each entry's operator checkpoints. Artifact edits follow the same hard limits proven in the notebook run: no renumbering, no folder moves, no content rewrites beyond what the recipe specifies, `[NEEDS CLARIFICATION]` + stop when a recipe can't be applied cleanly.
6. **Report:** framework files synced, migrations applied, files intentionally untouched, flags raised. Recommend the operator review the diff and commit.

### Migration entry format (sketch)

```markdown
## 001 — Frontmatter exports, depends_on, Build Notes, Modification sources
**Applies when base < <version/commit>.**
**Affects:** tickets.
**Recipe:** (the generalized content of the notebook's upgrade-tickets.md:
derive `introduces` from Terminology — excluding annotation entries;
derive `modifies` from Modifies targets; derive and CONFIRM `depends_on`
per ticket; normalize cross-ticket sources to [Ticket: NNN-slug]; trim
Modifies headings to canonical names, preserving glosses in Change text;
restructure non-schema Modifications to schema form, restating — never
inventing — Updated acceptance criteria.)
```

Entry 001 is effectively already written and battle-tested: generalize `notebook/agents/upgrade-tickets.md` plus the review fixes from its verification.

---

## Open questions (decide during build)

1. **VERSION format:** framework commit hash (precise, ties to history match) vs. integer (readable, matches migration numbering). Lean: integer that migration entries reference, mapped to commits inside MIGRATIONS.md.
2. **Pointer's source default:** pin the GitHub URL in the pointer at install time (with local-path override), or always ask? Lean: pin at install, agent confirms before fetching.
3. **MIGRATIONS.md vs migrations/ directory:** start with the single file; split if entries get long.
4. **Should `upgrade.md` itself be registered by the install agent as an invocable wrapper?** Lean: yes — it *is* the pointer (component 4 and the wrapper may be the same file).

## Out of scope

- Upgrading run state (`docs/features/`) or built product code — migrations operate on the fixture only.
- Automated (unattended) upgrades — the operator checkpoints are load-bearing; this is an assisted migration, not a background job.
- Downgrades.

## Implementation order

1. `agents/VERSION` + the process rule (stamp bumps and migration entries accompany schema changes) noted in MIGRATIONS.md's header.
2. `MIGRATIONS.md` seeded with entry 001.
3. `agents/upgrade.md`.
4. Install agent: add the upgrade pointer/wrapper.
5. README wiring.
6. Validate by re-running against a scratch copy of the notebook repo's initial commit — the output should reproduce (or improve on) the migration we already verified.
