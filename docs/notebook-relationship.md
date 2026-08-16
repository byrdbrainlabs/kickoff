# Kickoff ↔ Notebook

Context doc: how this repo relates to the `notebook` project. This is operator context, not a framework artifact — no agent reads this.

## The relationship (resolved 2026-08-16: merged)

**Kickoff** (this repo) was the framework: the requirements agents, their schemas, and the conventions. **Notebook** (`~/dev/notebook`) was its first real consumer — an AI-native writing environment whose specs are authored with these agents, and the proving ground on the way to a ~100-ticket backlog realistic enough to benchmark autonomous coding harnesses against ("backlog bench").

The two repos originally ran a deliberately coupled loop: spec Notebook → hit a framework weakness → fix it here → sync the fix (and any artifact migration) back into Notebook's vendored, byte-identical copy of `agents/`. With a single consumer, that separation stopped paying for itself: every framework change carried a sync-and-migrate tax (version detection by history match, drift checks, one-off migration agents, a planned permanent upgrade agent), while the genericity the split was meant to protect couldn't actually be validated against anything.

So the development locus merged into Notebook:

- **Notebook's `agents/` tree is now the framework's source of truth**, developed in place. Its `agents/README.md` carries the rules that keep it extractable (product-agnostic tree, schema changes migrate artifacts atomically in the same commit).
- **This repo is frozen** at the final sync below — kept as the framework's history and the seed for a future extraction, which happens when a second consumer project exists and genericity can be designed against two real consumers instead of one.
- **`plans/upgrade-agent.md` moved to Notebook and is parked** — the consumer-upgrade machinery it designs becomes relevant again exactly at extraction time.

## Sync log (closed)

| Date | Kickoff version | What happened |
|---|---|---|
| 2026-07-12 | initial-era | Notebook created with the then-current framework; tickets migrated to the exports / `depends_on` schema via one-off `upgrade-tickets.md` |
| 2026-08-16 | `88440d4` (HEAD, final) | **Final sync.** Verifiability enablers propagated: 6 framework files + README; 3 new Platform & Constraints commitments in Notebook's foundation (Semantic addressability, Initial state, Launch contract); stable `AC-` IDs assigned to all 240 EARS criteria across the 9 draft tickets; ticket 008 gained `supersedes: [AC-003.7, AC-003.26]`. Notebook then adopted the framework in place (its commits `e34a99b`, `cb62607`) — no further syncs will occur. |
