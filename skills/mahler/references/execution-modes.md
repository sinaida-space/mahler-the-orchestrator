# Execution Modes & Model Availability

Mahler adapts two things to the session's real conditions: **how much orchestration to use** (execution mode, driven by the user's remaining budget) and **which models to route to** (availability ladder, driven by what the subscription actually offers). Both are decided before the PRD and stated in it — the user approves the spend model together with the plan.

## What Mahler can and cannot know

Mahler **cannot** query the user's remaining token/usage limit — no tool exposes it. Budget state comes from exactly three sources, in priority order:

1. **The user says so** — "budget tight", "9% weekly left", "plenty of room". Always wins.
2. **Phase 0 asks** — the Blue Hat budget question (mandatory, see below).
3. **Runtime signals** — a spawn fails with a rate-limit error, a model is rejected as unavailable, responses start degrading. React immediately: drop one mode down, tell the user in one line, continue.

Never present a guess about remaining budget as fact. If the user doesn't know or doesn't answer, default to **Chamber** (the middle mode) — not Full Orchestra.

## The Blue Hat budget question (Phase 0, mandatory)

Alongside the GitHub questions, always ask:

> "How's your usage budget for this? Roughly: plenty (>40% of weekly left), moderate (15–40%), or tight (<15%)? This decides whether I run a full agent pipeline or implement more myself."

## The agent-justification rule (all modes, not just tight budget)

The core math: **every subagent costs fixed overhead before it does any work** — its system prompt, reading the spec, reporting back. A fleet of 14 implementers + verifiers can burn more on context overhead than on actual implementation. This holds at every budget, so the rule below applies in Full Orchestra too, not only when budget shrinks.

Before the PRD lists a **separate agent** for a task, it clears this bar:

- **Inline what is smaller than its own overhead.** A few lines, a rename, a one-file config edit — the orchestrator does it in-context. Spawning costs more than doing.
- **Merge by default.** Adjacent tasks become one agent unless they need different model tiers *or* touch conflicting files and must run in parallel worktrees. Three haiku config edits are one agent. Two sonnet edits to the same file are one issue.
- **A role earns a seat only if it catches an error class another role would miss.** A verifier that re-runs the exact DoD command the implementer already ran is not a second role, it is a second bill. One fresh-context verifier per *group* of merged tasks, not per task — per-task only where a task is flagged high-risk.
- **Overhead budget.** Estimated spawn overhead across the whole run stays under ~1/3 of the run's total estimated tokens. If the PRD's agent list breaks that, collapse it before presenting: merge tasks, drop per-task verifiers to per-group, fold scouts into the orchestrator.

## The three modes

When budget shrinks, the overhead-to-work ratio is what kills you, so the response is to collapse agents further, not to skip planning.

### 🎻 Full Orchestra — budget plenty (>40%)

The widest pipeline: scouts, implementers routed by model+effort, fresh-context verification, dedicated reviewer at Phase 4, parallel worktrees for disjoint groups. "Widest" still means **every spawn clears the agent-justification bar above** — Orchestra is permission to spawn where it pays, not one agent per row in the task table. Verification is per group by default; per-task only for high-risk tasks.

### 🎼 Chamber — budget moderate (15–40%), or unknown

Same phases, fewer bodies:

- **No scout fleet** — the orchestrator reads the codebase itself (it's cheaper than briefing a scout and reading its report, unless the codebase is huge)
- **Batch small tasks** — all haiku-tier work into one agent; merge adjacent same-file sonnet tasks into single issues
- **Batch verification** — one verifier run per *group* of merged tasks, not per task
- **Reviewer survives** — Phase 4 review is judgment the orchestrator that wrote nothing can't skip cheaply; but it doubles as the verifier for the final group
- **Effort tuned down one notch** where the task tolerates it (medium→low for near-mechanical work); never below the task's judgment floor
- Issue-first stays: specs are cheap, rework is not

### 🎹 Solo — budget tight (<15%)

The pattern from the field: **no subagent fleet at all.**

- The orchestrator implements the entire approved PRD itself, in one focused pass, in the current context
- File structure and task order from the PRD are kept — the plan still governs, only the delivery collapses
- One verification pass at the end (run the DoD commands, local server + screenshot for visual work), not per task
- GitHub hygiene compresses to **one tracking issue** holding the whole PRD as a checklist, checked off as work lands; per-task issues are skipped
- Commits still granular (one per PRD task) so history stays reviewable
- No Phase 4 reviewer agent — instead a single self-review pass against the reviewer axes (correctness, leaks, conflicts, security) before the final commit, honestly labeled as self-review in the summary
- Announce the mode in one line before starting, exactly like: *"Degrading gracefully per Mahler's token rules: no subagent fleet — I'll implement the approved PRD myself in one pass, verify once, commit locally. Same result, fraction of the cost."*

### Mode switching mid-run

Budget is spent while working; a run that started Full Orchestra may need to land Solo. Watch for the runtime signals above. On any of them: finish the in-flight agent, drop one mode, tell the user in one line. Never silently continue spawning into a rate limit.

## Model availability ladder

The fable/opus/sonnet/haiku hierarchy is the *ideal*, not an assumption. Subscriptions differ; models come and go. At Phase 1, before spawning anything, determine what's actually available — from what the user said, from the session's own model, and from spawn failures — and route with fallbacks:

| Ideal | If unavailable → | Adjustment |
|-------|------------------|------------|
| fable (creative direction) | orchestrator does Phase 1 itself | Already in the skill; same logic, same output format |
| opus (complex/judgment work) | sonnet at **high** effort | Sonnet-high covers most opus-tier tasks; flag genuinely hard math/architecture to the user as elevated-risk |
| sonnet (standard work) | opus at **medium** effort if that's what exists, else current model | Paying more per token but not more thinking |
| haiku (mechanical work) | sonnet at **low** effort | Low effort keeps it from over-thinking mechanical work |
| only the current model exists | Solo-style: no spawning, all phases in-context | The 5-phase structure still applies — it saves tokens regardless of who executes |

Two rules on top of the ladder:

- **Cost ranking is relative, not memorized.** Roughly haiku < sonnet < opus ≈ fable per token, but if the user's plan meters models differently (e.g., a flat allowance where opus burns limit 5× faster), ask once in Phase 0 Blue Hat and route to whatever is cheapest *for this user*. The Prime Directive is unchanged: cheapest resource that reliably completes the task — "resource" now meaning model × effort × the user's actual metering.
- **A failed spawn is data.** If a model errors as unavailable, update the ladder for the rest of the session — don't retry it per task.

## Where this surfaces in the PRD

The Phase 2 PRD gains two lines the user approves explicitly:

```markdown
## Execution Mode
- Mode: Chamber (budget: moderate, per your Phase 0 answer)
- Available models this session: sonnet, haiku (fable, opus unavailable → sonnet-high covers opus-tier tasks)
- Agent count: ~4 (2 batched implementers, 1 batched verifier, 1 reviewer) instead of 9
```

Approving the PRD approves the spend shape. If the user overrides ("actually go full pipeline"), that wins.
