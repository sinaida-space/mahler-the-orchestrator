# Execution Modes & Model Availability

Mahler adapts two things to the session's real conditions: **how much orchestration to use** (execution mode, driven by the task's shape) and **which models to route to** (availability ladder, driven by what the subscription actually offers). Both are decided before the PRD and stated in it — the user approves the spend model together with the plan.

## Lean by default — never ask about budget

The user always wants to save tokens. Mahler **never asks** how much budget or weekly limit is left; it cannot read the quota, and the question itself costs a round-trip and an interruption. Every run is token-lean by default.

Mode comes from the task, not from a budget answer. The user still gets the **choice of a bigger version**: the Phase 2 approval question offers the recommended mode plus the next size up and down, each with its token estimate and what it adds or drops. That is the only place size is asked.

1. **The user says so** — "tight", "go wide", "solo", "full pipeline". Always wins.
2. **Task shape** — the architecture pattern from Phase 1 picks the mode (see table below).
3. **Runtime signals** — a spawn fails with a rate-limit error, a model is rejected as unavailable, the user flags the limit. Drop one mode, say so in one line, continue.

| Task shape (Phase 1 pattern) | Mode |
|------------------------------|------|
| single call, or ≤ ~3 small tasks | 🎹 Solo |
| chain / reflection loop / multi-agent (the usual case) | 🎼 Chamber (**default**) |
| large project with disjoint parallel groups | 🎻 Full Orchestra, offered as "bigger" at approval; recommended only when parallelism clearly pays |

## Think high, read mid, do cheap

The cost split that makes Mahler worth running. Every token the orchestrator reads is re-read on every later turn, at the orchestrator's (most expensive) price. So:

| Work | Model | Effort | Why |
|------|-------|--------|-----|
| **Think** — interrogation, pattern choice, decomposition, specs, fork resolution, final review judgment | strongest available (fable → opus → sonnet-high) | high | Quality is decided here; it is short output over a small, curated context |
| **Read** — map a codebase, digest docs/PDFs/long files, read logs, survey a backlog, inspect an existing project | sonnet | low | Sonnet reads bulk context at a fraction of the price and returns a ≤15-line digest; the thinker only ever sees the digest |
| **Locate** — find files, list, grep, count, check a path exists | haiku | low | Zero judgment |
| **Do** — standard implementation, tests, docs, CSS/HTML/JS | sonnet | medium | The default implementer |
| **Do (mechanical)** — boilerplate, renames, config, git/gh, formatting, commits, pushes | haiku | low | Batch into one agent |
| **Do (hard)** — GLSL/shader math, architecture, hard debugging, perf | opus | medium–high | Only where sonnet would likely fail |
| **Verify** — run DoD commands, screenshot, report pass/fail | haiku (sonnet if judging visuals) | low | A command runner, not a thinker |

Rules:

- **The thinker never bulk-reads.** Anything over ~300 lines or more than 3 files to understand goes to a sonnet reader with a specific question and a required format (files, lines, contracts, traps). Reading a few small files directly is fine — cheaper than a spawn.
- **Readers bring facts, not decisions.** The thinker decides.
- **Readers run in parallel** when their questions are independent, in one message.
- **The orchestrator session's own model is the thinker.** If the session runs on sonnet, planner work still goes to `opus` via the Agent tool's `model` parameter.

## The agent-justification rule (all modes)

The core math: **every subagent costs fixed overhead before it does any work** — its system prompt, reading the spec, reporting back. A fleet of 14 implementers + verifiers can burn more on context overhead than on actual implementation. The rule below applies in every mode.

Before the PRD lists a **separate agent** for a task, it clears this bar:

- **Inline what is smaller than its own overhead.** A few lines, a rename, a one-file config edit — the orchestrator does it in-context. Spawning costs more than doing.
- **Merge by default.** Adjacent tasks become one agent unless they need different model tiers *or* touch conflicting files and must run in parallel worktrees. Three haiku config edits are one agent. Two sonnet edits to the same file are one issue.
- **A role earns a seat only if it catches an error class another role would miss.** A verifier that re-runs the exact DoD command the implementer already ran is not a second role, it is a second bill. One fresh-context verifier per *group* of merged tasks, not per task — per-task only where a task is flagged high-risk.
- **Overhead budget.** Estimated spawn overhead across the whole run stays under ~1/3 of the run's total estimated tokens. If the PRD's agent list breaks that, collapse it before presenting: merge tasks, drop per-task verifiers to per-group, fold small reads into the orchestrator.

## The three modes

Smaller modes collapse agents, never planning. The overhead-to-work ratio is what kills a run.

### 🎻 Full Orchestra — the "bigger" option at approval

The widest pipeline: readers, implementers routed by model+effort, fresh-context verification, dedicated reviewer at Phase 4, parallel worktrees for disjoint groups. "Widest" still means **every spawn clears the agent-justification bar above** — Orchestra is permission to spawn where it pays, not one agent per row in the task table. Verification is per group by default; per-task only for high-risk tasks.

### 🎼 Chamber — the default

Same phases, fewer bodies:

- **Few readers** — one or two sonnet-low readers for bulk context (codebase map, docs), returning digests; the thinker reads only small files directly
- **Batch small tasks** — all haiku-tier work into one agent; merge adjacent same-file sonnet tasks into single issues
- **Batch verification** — one verifier run per *group* of merged tasks, not per task
- **Reviewer survives** — Phase 4 review is judgment the orchestrator that wrote nothing can't skip cheaply; but it doubles as the verifier for the final group
- **Effort tuned down one notch** where the task tolerates it (medium→low for near-mechanical work); never below the task's judgment floor
- Issue-first stays: specs are cheap, rework is not

### 🎹 Solo — small tasks, or when the limit bites

The pattern from the field: **no subagent fleet at all.**

- The orchestrator implements the entire approved PRD itself, in one focused pass, in the current context
- File structure and task order from the PRD are kept — the plan still governs, only the delivery collapses
- One verification pass at the end (run the DoD commands, local server + screenshot for visual work), not per task
- GitHub hygiene compresses to **one tracking issue** holding the whole PRD as a checklist, checked off as work lands; per-task issues are skipped
- Commits still granular (one per PRD task) so history stays reviewable
- No Phase 4 reviewer agent — instead a single self-review pass against the reviewer axes (correctness, leaks, conflicts, security) before the final commit, honestly labeled as self-review in the summary
- Announce the mode in one line before starting, exactly like: *"Degrading gracefully per Mahler's token rules: no subagent fleet — I'll implement the approved PRD myself in one pass, verify once, commit locally. Same result, fraction of the cost."*

### Mode switching mid-run

A run that started as Chamber may need to land Solo. Watch for the runtime signals above. On any of them: finish the in-flight agent, drop one mode, tell the user in one line. Never silently continue spawning into a rate limit.

## Model availability ladder

The planner/opus/sonnet/haiku hierarchy is the *ideal*, not an assumption. Subscriptions differ; models come and go. At Phase 1, before spawning anything, determine what's actually available — from what the user said, from the session's own model, and from spawn failures — and route with fallbacks:

| Ideal | If unavailable → | Adjustment |
|-------|------------------|------------|
| planner (creative direction) | strongest spawnable model: fable → opus → sonnet at **high** → orchestrator does Phase 1 itself | Planning is where quality is decided, so it always gets the top available rung; aliases resolve to the newest release in each family |
| opus (complex/judgment work) | sonnet at **high** effort | Sonnet-high covers most opus-tier tasks; flag genuinely hard math/architecture to the user as elevated-risk |
| sonnet (standard work) | opus at **medium** effort if that's what exists, else current model | Paying more per token but not more thinking |
| haiku (mechanical work) | sonnet at **low** effort | Low effort keeps it from over-thinking mechanical work |
| only the current model exists | Solo-style: no spawning, all phases in-context | The 5-phase structure still applies — it saves tokens regardless of who executes |

Two rules on top of the ladder:

- **Cost ranking:** haiku < sonnet < opus ≈ fable per token. Don't ask the user about metering; if they volunteer that their plan meters differently, route to whatever is cheapest *for them*. The Prime Directive is unchanged: cheapest resource that reliably completes the task — "resource" now meaning model × effort × the user's actual metering.
- **A failed spawn is data.** If a model errors as unavailable, update the ladder for the rest of the session — don't retry it per task.

## Where this surfaces in the PRD

The Phase 2 PRD gains two lines the user approves explicitly:

```markdown
## Execution Mode
- Mode: Chamber (default; task is a chain of 4 tasks)
- Available models this session: sonnet, haiku (fable, opus unavailable → planner and opus-tier tasks run on sonnet-high)
- Agent count: ~4 (2 batched implementers, 1 batched verifier, 1 reviewer) instead of 9
```

Approving the PRD approves the spend shape. If the user overrides ("actually go full pipeline"), that wins.
