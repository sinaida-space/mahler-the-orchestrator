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

## Effort Defaults

- Fable / Orchestrator: `high` (never ultrathink by default; `xhigh` only for genuinely hard architectural forks)
- Sonnet implementers: `medium` (medium Sonnet 5 ≈ high Sonnet 4.6 for implementation tasks)
- Sonnet verifiers: `low` (they run a command and report what they see — no thinking needed)
- Escalate to `high` for a Sonnet implementer only after verifier has rejected it twice with diagnosis "didn't think hard enough"

## Communication Discipline

- One short pipeline status per turn: todo list with done / in progress / blocked
- Don't paraphrase agent reports back to the user — only the decision and next action
- Don't claim progress on things you haven't verified in tool results this session
- When limit is low: raise effort, merge tasks, don't skip writing specs
