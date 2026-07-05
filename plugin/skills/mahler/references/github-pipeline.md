# GitHub Pipeline

Issue-first orchestration. Every task becomes a GitHub issue with a complete spec before any agent touches code.

## Issue Spec Template

```markdown
**Model / Effort:** e.g. `sonnet / medium`. Both assigned by Fable per model-routing.md —
independent axes, not one decision. Carried verbatim into the dispatch envelope; the
implementer never picks its own effort.

**Goal:** One sentence — what the user sees after merge.

**Context:** Files and lines to change; traps (duplicates, generated files,
non-obvious dependencies). Everything the implementer needs to NOT research independently.

**Contract:** Exact formats at task boundaries — data schemas, function signatures,
message/file formats, field names, error codes. With example values, not descriptions.

**Diagram:** If the task involves data flow, call order, or 2+ components interacting —
ASCII or mermaid inline. Skip for trivial single-file edits.

**Resolved forks:** Each decision + one-line rationale. No open questions.

**Steps:** Numbered plan by file.

**Boundaries:** What NOT to do. (Don't refactor surrounding code. Don't touch generated files.
Don't add beyond the spec.)

**DoD + verification:**
- [ ] Checklist item
- [ ] Checklist item
- Exact command to run: `npm test`, `open index.html and click X`, `gh workflow view`
- Expected output: what you should see when it passes
```

**Readiness test:** Can the implementer execute without opening any file for research?
If no → the spec is incomplete.

**Effort check:** if Effort is `high` because a fork is still open, that's a spec gap — resolve
the fork above instead. `high` should only survive for judgment that genuinely can't be
pre-resolved (numerical correctness, live debugging).

## GitHub Setup (Blue Hat output from Phase 0)

Before dispatch, ensure:
```bash
# Check repo exists
gh repo view

# If not: initialize
git init && gh repo create <name> --private --source=.

# Create project board if needed
gh project create --owner @me --title "<project name>"

# Apply standard labels
gh label create "blocked" --color "e11d48"
gh label create "in-progress" --color "f59e0b"
gh label create "needs-spec" --color "6366f1"
```

Ask the user about branching strategy in Phase 0 (Blue Hat). Default recommendation:
- **Solo, fast iteration**: direct commits to main, tags at milestones (`v0.1`, `v1.0`)
- **Multiple collaborators**: feature branches, PR per issue
- **Experimental / risky changes**: always a branch regardless

## Dispatch Envelope

Full spec lives in the issue body. Implementer prompt is a short operational envelope.
Read `${CLAUDE_PLUGIN_ROOT}/skills/mahler/references/prompt-quality.md` for the full block library — compose from there.

```xml
You are the implementer. Working dir: <absolute-path>. Effort: <low|medium|high — from
the issue's Model/Effort line, set explicitly here, not left to your default>.
You implement exactly what the spec says — nothing more.

Your spec: run `gh issue view <N>` and execute it strictly.

<investigate_before_answering>
Never speculate about code you have not read. Read relevant files before making any
claims. Give grounded, hallucination-free answers only.
</investigate_before_answering>

<default_to_action>
Implement changes rather than suggesting them. Resolve ambiguity by reading the spec
or reading the code — do not ask questions or guess.
</default_to_action>

<scope_discipline>
Only make changes the spec asks for. Do not refactor surrounding code, add docstrings
to code you didn't change, or design for hypothetical future requirements.
</scope_discipline>

<use_parallel_tool_calls>
Call all independent tools in parallel. When reading multiple files, read simultaneously.
Only sequence tool calls when a later call depends on an earlier result.
</use_parallel_tool_calls>

<context_continuity>
Your context may be automatically compacted. Do not stop early — save progress to your
report file before any context refresh. Complete the task fully.
</context_continuity>

On completion:
1. Run the DoD verification from the spec. Fix any failures before reporting.
2. One conventional commit to main with "(#N)" at the end of the message.
   FORBIDDEN: "closes #N", "fixes #N" — auto-close before verification is a bug.
3. Do NOT close or comment on the issue.

Write your FULL report to: <scratchpad-path>/reports/<your-name>.md
Send me a digest ≤15 lines + the path to your report file.
Every fact I need to make a decision must be inline in the digest — not "see the file."
```

## Parallelism by File Overlap

**Same-file tasks** → sequential. Direct commits to main. No worktrees needed.

**Disjoint-file groups** → parallel in worktrees:
```
# Orchestrator determines groups by diffing planned file edits
# Group A: src/shader.glsl, src/main.js
# Group B: public/index.html, public/style.css
# No overlap → spawn both in parallel worktrees
```

Use `isolation: "worktree"` when spawning parallel implementers. Merge order decided by orchestrator after all verify.

## Verifier Prompt

```xml
You are the verifier for issue #<N>. Working dir: <path>. Effort: low.
You run the DoD check and report what you observe — you do not review code.

<do_not_act_before_instructions>
Do not modify files. Read the DoD command from the spec, run it, report the result.
</do_not_act_before_instructions>

Run exactly the verification command from the DoD section of `gh issue view <N>`.
Return: passed or failed, and exactly what you observed — no interpretation.
Browser checks: headless only (playwright/puppeteer --headless). Do not open a visible browser.

Write your result to: <scratchpad-path>/reports/verify-<N>.md
Send me: pass/fail + one-line summary of what you saw.
```

## Escalation Ladder

| Failure # | Action |
|-----------|--------|
| 1st | Same implementer, same model, raise to `effort: high` if not already there + verifier's exact failure list |
| 2nd | Same implementer at `effort: high`; escalate model tier only if verifier's diagnosis points at capability, not thinking depth |
| 3rd | Fresh implementer (new context), `effort: high` + verifier's full diagnosis |
| 4th | Label `blocked`, stop this task, short diagnosis to user, continue pipeline on independent tasks |

Fresh implementer on 3rd failure is specifically for cases where the original implementer's context has drifted. A new agent reading the spec fresh often unblocks immediately.

Never jump to `xhigh`/`ultrathink` anywhere on this ladder without surfacing the cost tradeoff to the user first — it isn't part of the default escalation path.

## Acceptance

Only a Sonnet hand closes, after verifier passes:
```bash
gh issue close <N> --comment "<SHA> — <verifier verdict in one line>"
```

Rules:
- Never close from a commit message
- Never close before verification
- Never let the implementer close their own task
- Issue body is never modified after dispatch — it's the spec, not a log

## Async Spec-Ahead

While an implementer works, Fable writes specs for the next tasks in the queue.
Before dispatching a pre-written spec, do a one-line sanity check: does the actual diff from the previous task match what the pre-written spec assumed? If not, update the spec before dispatch.

## Grounding Gate (for synthesis tasks)

Any spec whose output is a synthesis of sources (guide, digest, summary, "extracted advice") must include:

1. **Source of truth** — path to the deepest available source (transcript, not derivative; original, not paraphrase)
2. **DoD requires literal diff against source** — every claim must have a pointer (timecode, URL, file:line) verified against the source, not against another derivative
3. **Verifier checks connective tissue** — words added during compression ("when", "always", "after", "most", "therefore") are where hallucinations appear
4. **Derived-vs-derived doesn't count** — two consistent copies of the same paraphrase ≠ truth
