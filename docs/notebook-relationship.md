# Kickoff ↔ Notebook

Context doc: how this repo relates to the `notebook` project, and the process that couples them. This is operator context, not a framework artifact — no agent reads this.

## The relationship

**Kickoff** (this repo) is the framework: the requirements agents, their schemas, and the conventions. **Notebook** (`~/dev/notebook`) is its first real consumer — an AI-native writing environment whose specs are authored with these agents. Notebook is also the proving ground: the point of writing Notebook's backlog is partly the product, and partly to pressure-test Kickoff itself on the way to a ~100-ticket backlog realistic enough to benchmark autonomous coding harnesses against ("backlog bench").

That gives the two repos a deliberately coupled, co-evolving loop:

1. **Spec Notebook.** Run the agents inside the notebook repo to flesh foundation and tickets.
2. **Hit a framework weakness.** Speccing real tickets surfaces gaps — a schema that can't express something, an agent instruction that misfires, a missing convention.
3. **Fix it here.** The framework change is made in Kickoff — agents, schemas, README — never by hand-editing Notebook's copies.
4. **Sync it back.** The change is propagated into Notebook, and any existing Notebook artifacts (tickets, foundation) are migrated to the new contract.

The invariant: **Kickoff is the single source of truth for the framework.** Notebook's `agents/` tree is a vendored, byte-identical copy of some Kickoff commit — a full copy, not the thin pointer wrappers `install.md` creates for harness registration. If Notebook's copies ever drift from every historical Kickoff version, someone hand-edited them, and that drift must be resolved before any sync overwrites it.

## The sync process (manual, for now)

1. **Find Notebook's framework version.** Diff `notebook/agents/**` against this repo's git history until a commit matches byte-for-byte. (Precedent: Notebook's initial import matched exactly, which is what makes this reliable.)
2. **Copy the changed files** from Kickoff HEAD into Notebook — framework files only; never touch `docs/` content Notebook owns.
3. **Migrate existing artifacts** if a schema changed. Migration is done by a **one-off migration agent** written for that specific delta and deleted after use — see `notebook/agents/upgrade-tickets.md` (the July 2026 migration to the exports / `depends_on` era) as the template. Migrations adjust format and metadata only; they never re-litigate deliberately-authored content.
4. **Apply foundation deltas.** If the foundation schema gained sections or Platform & Constraints items, add them to Notebook's `docs/foundation.md` (schema defaults usually suffice; confirm with the operator).

The manual process is scheduled for replacement: `plans/upgrade-agent.md` designs a permanent `agents/upgrade.md` that lives in this repo, always executes from the *new* framework version (avoiding the stale-upgrader paradox), detects the consumer's version by history match, refuses to clobber drift, and applies recorded migrations. An `agents/VERSION` stamp will make version detection instant. Until that exists, this document is the process.

## Sync log

| Date | Kickoff version | What happened |
|---|---|---|
| 2026-07-12 | initial-era | Notebook created with the then-current framework; tickets migrated to the exports / `depends_on` schema via one-off `upgrade-tickets.md` |
| — | `385ab00` | **Notebook currently sits here** |
| pending | `72cfdf8` | Verifiability enablers not yet synced: 6 framework files (kickoff / feature / documentation agents; foundation / ticket / feature-doc schemas), 3 new Platform & Constraints items for Notebook's foundation (semantic addressability, initial state, launch contract), and a one-off migration to assign `AC-` criterion IDs to Notebook's 9 draft tickets |

Keep this table current: add a row when a sync lands, and a `pending` row when Kickoff moves ahead of Notebook.
