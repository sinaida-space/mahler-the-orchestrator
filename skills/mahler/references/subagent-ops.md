# Subagent Operations

Battle-tested patterns from production sessions. Read before dispatching any subagent.

## Scratchpad Protocol (mandatory)

Every subagent writes a full report to a file before finishing.

**Path:** `<scratchpad-session>/reports/<agent-name>.md`

The orchestrator provides this path explicitly in every dispatch prompt. The scratchpad path is in the system prompt (see session environment).

**Digest rule:** The final message sent back must be a self-sufficient digest ≤15 lines + the path to the full report.

- "Self-sufficient" means: every fact the orchestrator needs to make a decision is inline in the digest — quotes, dates, numbers, verdicts. Not "see the file for the decision."
- The file contains full detail with coordinates (file:line, timestamps, command output). It's for archaeology, not real-time judgment.
- This pattern means a lost notification doesn't require a re-ask — the orchestrator reads the file.

## Typed Digest by Role (artifact contract)

The digest a subagent sends back is a **typed block with fixed fields**, not free prose. The orchestrator's context stays small because it consumes fields, not paragraphs it has to re-parse; Phase 4 integration reads the same shape from every task. Free text only for something the schema has no field for, capped at 5 lines under the block.

**Scout**
```yaml
scout:
  question: <the question you were given>
  findings: [<fact with file:line or coordinate>, ...]
  contracts: [<signature / data shape found>, ...]
  traps: [<gotcha the implementer must know>, ...]
  unknowns: [<what you could not determine>, ...]
```

**Implementer**
```yaml
implementer:
  issue: <N>
  changed_files: [<path>, ...]
  commit: <sha>
  dod_check: pass | fail | not_run
  deviations: [<where you departed from the spec and why>, ...]
  follow_ups: [<noticed, out of scope>, ...]
```

**Verifier**
```yaml
verifier:
  issue: <N>
  command: <exact command run>
  result: passed | failed
  observed: <one line — error text or success marker>
```

**Reviewer**
```yaml
reviewer:
  deterministic:            # from Step 0, before any code reading
    build: pass | fail | n/a
    lint:  pass | fail | n/a
    types: pass | fail | n/a
    tests: <passed>/<total> | n/a
    prose: pass | fail | n/a
  findings:
    - severity: high | med | low
      at: <file:line>
      scenario: <input X produces wrong output Y>
      fix: <one line>
  verdict: clean | fixes_required
```

**Agent-to-agent handoff:** Pass the path to the previous agent's report file in the next agent's dispatch prompt. Don't route large data through the orchestrator's context twice.

**Scratchpad is a bus, not storage:** Durable artifacts (specs, docs, deliverables) live in the repo or issue body. The scratchpad is inter-agent wire.

## Lost Report Recovery

An agent finishes but sends only an idle notification with no digest.

**First move:** `SendMessage(to: <agent-id>, "Please send your report digest and path to your report file via SendMessage to main.")` — this works reliably.

**If agent is dead:** Read `<scratchpad>/reports/<agent-name>.md` directly. The file should exist if the agent followed protocol before dying.

**If file also missing:** Spawn a recovery scout: `git log --oneline -10 && git status` — partial work is often correct and should be accepted and continued, not redone.

## Session Death Respawn

An agent dies mid-task (session limit hit).

Respawn a replacement with explicit instructions to audit the predecessor's traces:
```
Your predecessor started this task and may have been killed mid-execution.
Before doing anything: run `git log --oneline -5`, `git status`, and check
<scratchpad>/reports/<predecessor-name>.md if it exists.
Partial work may be correct — accept and complete it; don't redo from scratch.
Your spec: `gh issue view <N>`
```

## Headless Browser Rule

Verifiers and implementers that need browser checks **must use headless only**.
- playwright/puppeteer: `--headless` flag always
- Never use chrome-devtools MCP or any tool that opens a visible Chrome window
- The user is working in their own Chrome session — stealing focus is unacceptable

Add to any dispatch prompt that involves visual verification:
```
Browser checks: headless only (playwright/puppeteer with --headless).
Do NOT open a visible browser window.
```

## Judgment Boundary

Scouts and implementers get eyes and hands. Never the head.

**What scouts may do:** find, list, measure, quote, count, grep, run, check
**What scouts may NOT do:** choose, recommend, rank, prioritize, conclude, decide

If a scout returns a recommendation: treat it as raw material. Re-derive the decision yourself. Don't copy scout recommendations into specs without your own reasoning.

When a scout needs to present N options: return all N with objective attributes (dates, sizes, line counts, dependency counts). You pick.

## Effort Routing

Effort is assigned **per task**, not per role — read `${CLAUDE_PLUGIN_ROOT}/skills/mahler/references/model-routing.md` for the full decision tree. Fable sets it in the PRD's Effort column (Phase 2); the dispatcher carries it into every implementer and verifier prompt explicitly — it is never left to a model's default.

Role-level floors and ceilings (the task-level rubric picks the exact value within these):
- **Fable / Orchestrator**: always `high` — never lower (judgment is the whole job), never `xhigh`/`ultrathink` by default
- **Implementers**: `low`–`high`, task-dependent. `medium` is the common case for a complete spec; `low` only for zero-judgment mechanical work; `high` only when judgment survives into execution
- **Verifiers**: always `low` — they run a command and report what they see, never interpret
- **Reviewer** (Phase 4, `agents/reviewer.md`): always `high` — reading a full merged diff for correctness bugs and cross-task conflicts is judgment-heavy, never route it lower. Distinct from a verifier: reviewer reads code, verifier only runs a command.
- **Escalation to `high`** for an implementer happens on the ladder (see github-pipeline.md), specifically after the verifier's diagnosis says "didn't think hard enough," not preemptively

## Communication Discipline

- One short pipeline status per turn: todo list with done / in progress / blocked
- Don't paraphrase agent reports back to the user — only the decision and next action
- Don't claim progress on things you haven't verified in tool results this session
- When limit is low: drop an execution mode (see `${CLAUDE_PLUGIN_ROOT}/skills/mahler/references/execution-modes.md`) — collapse agents first, lower effort second; never skip writing specs to compensate
