# Context Budget

Every tool call re-sends the whole conversation. Cost per turn ≈ current context size, so an agent's total cost grows with **turns × context**, roughly quadratically. Cache hits make each re-read cheaper, never free: a 98% cache-hit run can still spend almost all of its limit on cache reads.

Measured failure (conspace-rooms, 2026-09): one `mahler:developer` ran 270 turns, context grew 46k → 241k, 44M cache-read tokens, 67% of the session. Causes: 21 tool results over 6 KB each (5.7 MB of raw output: profiler dumps, CDP logs, page text), a debug loop inside the implementer, `sleep` holds via Monitor, effort `high`.

## Hard limits (every subagent)

| Limit | Value | On hit |
|-------|-------|--------|
| Tool calls | 40 (verifier 10, assistant 20) | write handoff, stop |
| Context | ~120k tokens (visible when a turn feels heavy, a file you read twice, or 30+ calls) | write handoff, stop |
| Same failing check | 2 attempts | stop, report as `dod_check: fail` with the observed error |

Stopping at a limit is success, not failure. The orchestrator continues with a fresh agent.

## Relay: split context instead of growing it

When an agent stops at a limit, it writes `<scratchpad>/reports/<agent-name>.handoff.md`:

```
done: [<what is finished and committed, with SHA>]
state: <uncommitted changes? branch? servers running?>
next: [<concrete next steps, in order>]
facts: [<file:line, signatures, numbers the next agent would otherwise re-discover>]
dead_ends: [<what was tried and failed, one line each>]
```

The orchestrator spawns a **fresh** agent of the same tier with: the original envelope + "Read `<handoff path>` first; don't redo `done`." The new agent starts at base context (~45k), not at 240k. Max 3 relays per task, then `blocked`.

## Output hygiene (the biggest lever)

Raw output that enters context is paid on every turn after it.

- Pipe anything that may exceed ~50 lines to a file, then read only what you need: `cmd > $SP/log.txt 2>&1; tail -n 30 $SP/log.txt` or `grep -n ERROR`.
- Always cap: `| head -n 40`, `| tail -n 40`, `head -c 4000`. Never `cat` a file over 200 lines; use `grep -n` then `sed -n A,Bp`.
- Browser/profiler/CDP scripts print a **summary line** (fps, error count, pass/fail), never the full dump. Screenshots go to a file; describe or zoom, don't embed repeatedly.
- Never read the same file twice. Note the facts you need the first time.
- Batch independent reads and commands into one call (`a; b; c`), one round trip instead of three.
- No `sleep`, no Monitor holds, no polling loops inside a subagent. Wait for a condition with a single bounded command (`for i in $(seq 30); do <check> && break; sleep 2; done`; macOS has no `timeout`).

## Split roles to keep contexts short

- **Implementers don't debug in the browser.** They build, run the DoD command once, commit, report. A failing visual/perf check goes to the verifier and back through the escalation ladder, each step in a fresh context.
- **Investigation goes to a scout.** If the spec needs profiling or root-cause work first, that's a separate scout (report ≤15 lines), then a fresh implementer with the findings in its envelope.
- **Orchestrator stays thin.** It doesn't run browser checks itself (30+ browser calls in main is the same leak). It dispatches, reads digests, decides. After each phase, if main context passes ~100k, write a handoff to the scratchpad and tell the user one line: "Context is heavy: `/compact` or start a fresh session with `<handoff path>`."

## Effort

`high` effort makes agents explore more and stop later. Default implementers to `medium`; `high` only for math correctness or genuinely open judgment, and then with a tool-call limit of 30.
