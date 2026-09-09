# Architecture Patterns — pick the shape before decomposing

Phase 1 does this first, before any task breakdown. Fable names the pattern the task
actually needs and the reason, and writes both into the PRD above the task table. Most
tasks are not multi-agent work; saying so out loud is what stops the fleet spawning by
reflex.

## The decision table

| Task shape | Pattern | How Mahler runs it |
|---|---|---|
| One well-scoped output, quality is checkable in one pass | **single call** | one implementer, no scouts, no verifier fleet, no reviewer — the orchestrator checks the DoD itself |
| Output needs iterative revision against a rubric (copy, translation, security-sensitive code) | **reflection loop** | one implementer plus an evaluator pass against an explicit rubric; 2–4 iterations, hard cap, stop rule |
| A fixed sequence of narrow transforms, each feeding the next | **chain** | ordered issues with a gate between each — the previous task's DoD is the next task's entry contract; a failed gate routes to a fallback, never forward |
| Genuinely independent concerns needing different expertise or models | **multi-agent** | parallel implementers, one per file-disjoint group, merged by the orchestrator |
| The same facts or entities are read by more than one task, or across more than one session | **graph** | add one task that stands up a persistent store (see below); other tasks read and write it instead of passing summaries |

## The five rules (from the playbook)

1. **Start with the cheapest pattern that satisfies the task.** A single call is cheaper
   than a loop, a loop than a chain, a chain than multi-agent, multi-agent than a graph.
2. **Add the next pattern only when a specific, measured failure demands it** — not
   because the task "looks complex". Name the failure the added structure buys down.
3. **Match structure to risk.** High-stakes work (anything hard to undo) uses the
   predictable patterns — single call, chain. Loops and multi-agent tolerate more
   uncertainty.
4. **Count tokens, not agents.** A three-agent run at 20k tokens each costs the same as
   one agent at 60k. The question is never "how many agents" but "does this agent catch
   an error class another agent would miss".
5. **The graph earns itself.** Only when the same entity or relationship is queried by
   more than one task or across sessions. A store written once and never read back is a
   database table with extra overhead — use a plain file.

## When the pattern is "graph": scaffolding a store

Mahler does not carry a knowledge graph. It can **plan a task that builds one** when
rule 5 is met. That task's spec:

- **Storage: typed JSON or SQLite.** Never Neo4j or a graph database unless the user
  asks for one by name — for the scale these projects hit, it is overhead without payoff.
- **Minimal schema:** entities as nodes with a type; relationships as typed edges;
  every edge carries its provenance (source + date). Writes are additive — a changed
  fact is a new version with a `supersedes` edge, never an overwrite.
- **One writer task** defines the schema and the read/write helpers. Downstream tasks
  call those helpers; they never open the store raw.
- **Cross-session only.** If the whole job finishes in one session and one context
  window, the store is not needed — that is a loop, not a graph.

State this in the PRD as its own row, with the entities and edges it will hold, so the
user approves building it.
