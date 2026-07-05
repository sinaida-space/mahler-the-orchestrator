# Model Routing Table

Two independent dimensions per task: **model tier** (which model) and **effort level** (how hard it thinks). Route both — a hard task on the right model still needs the right effort, and an easy task on a capable model wastes tokens at high effort.

## Effort Decision Tree

```
Is the task's shape fully specified — one clear path, no design judgment needed
(rename, boilerplate, copy a pattern, mechanical edit)?
  → YES → low

Is it standard implementation with a few reasonable approaches, but the spec
already resolved the forks (issue-first spec exists)?
  → YES → medium

Does it require weighing tradeoffs the spec didn't resolve, non-obvious debugging,
math/algorithm correctness, or architecture that affects other tasks?
  → YES → high

Is this Fable doing decomposition, fork-resolution, or spec-writing (judgment, not code)?
  → YES → high (never xhigh/ultrathink by default)
```

**Effort is not a proxy for model tier.** A haiku-tier file rename is low effort by shape, not because haiku is cheap. An opus-tier shader can still be medium effort if the spec fully resolved the math and it's implementing a known pattern. Judge the task's ambiguity, not its model tier.

| Effort | Use for | Signal |
|--------|---------|--------|
| low | Verifiers (run a command, report), mechanical edits, renames, config | Zero judgment calls in execution |
| medium | Default for implementers with a complete issue spec | Spec resolved the forks; agent follows steps |
| high | Fable (always), or an implementer facing unresolved tradeoffs, hard debugging, math/algorithm correctness | Judgment calls remain at execution time |
| xhigh/ultrathink | Never by default | Only if a high-effort agent failed twice and the verifier's diagnosis is "didn't think hard enough" |

## Routing Decision Tree

```
Is this task mechanical (formatting, renaming, config, boilerplate)?
  → YES → haiku
  → NO ↓

Is this task standard implementation (web features, tests, docs, API integration)?
  → YES → sonnet
  → NO ↓

Is this complex (architecture, shaders, algorithms, hard debugging, system design)?
  → YES → opus
  → NO → sonnet (default to medium tier when uncertain)
```

## Detailed Routing by Domain

### Creative / Conceptual
| Task | Model | Rationale |
|------|-------|-----------|
| Brainstorm concepts, visual direction | fable | Creative strength, cheapest |
| Art references, mood boards, naming | fable | Doesn't need technical depth |
| Task decomposition, project planning | fable | Strategic thinking, low token cost |

### Shaders & Graphics
| Task | Model | Rationale |
|------|-------|-----------|
| GLSL fragment/vertex shader writing | opus | Math-heavy, precision matters |
| Shader optimization | opus | Requires deep understanding |
| Simple uniform/parameter setup | sonnet | Standard patterns |
| Copying shader boilerplate | haiku | Mechanical |

### Web Development
| Task | Model | Rationale |
|------|-------|-----------|
| React/Vue component architecture | sonnet | Standard patterns |
| Complex state management, WebSocket | opus | Architecture decisions |
| CSS styling, layout | sonnet | Standard |
| HTML boilerplate, meta tags | haiku | Mechanical |
| API route implementation | sonnet | Standard patterns |

### TouchDesigner
| Task | Model | Rationale |
|------|-------|-----------|
| Python script for complex logic | opus | Algorithm design |
| Standard CHOP/TOP/SOP setup | sonnet | Pattern matching |
| Parameter configuration | haiku | Mechanical |

### Infrastructure
| Task | Model | Rationale |
|------|-------|-----------|
| Docker, CI/CD pipeline design | sonnet | Standard patterns |
| package.json, tsconfig, configs | haiku | Mechanical |
| Performance profiling strategy | opus | Analytical depth |
| File renaming, restructuring | haiku | Mechanical |

### Documentation
| Task | Model | Rationale |
|------|-------|-----------|
| Architecture docs, design decisions | sonnet | Needs understanding |
| API documentation | sonnet | Standard |
| README, changelog, comments | haiku | Mechanical |

## Detailed Effort by Domain

| Task | Effort | Rationale |
|------|--------|-----------|
| GLSL shader math, novel algorithm | high | Correctness depends on judgment, not pattern-matching |
| Shader boilerplate from a resolved spec | medium | Pattern is known, spec resolved the approach |
| Complex state management design | high | Tradeoffs not yet resolved by the spec |
| Standard CRUD/API route from spec | medium | Spec + contract already fixed the shape |
| CSS/HTML boilerplate, config, renaming | low | No judgment in execution |
| Verifier running a DoD command | low | Execute and report, no interpretation |
| Fable: decomposition, fork resolution, spec writing | high | This is where judgment lives — never route Fable to low |
| Implementer, 2nd escalation retry | high | Verifier flagged reasoning gap, not a mechanical miss |

## Escalation Rules

**Model escalation:**
If a haiku agent returns an error or incomplete result, escalate to sonnet.
If a sonnet agent hits a wall on a subtask, escalate to opus.
Never escalate fable work — if fable is unavailable, the orchestrator handles it.

**Effort escalation (independent of model):**
First failure → same model, raise effort one level before considering a model change.
Second failure at high effort → fresh agent (see escalation ladder in github-pipeline.md), still at high — do not jump to xhigh/ultrathink without user awareness that cost is rising sharply.
Never raise effort as a substitute for a missing spec — if the agent is guessing because the issue body is incomplete, fix the spec, not the effort.

## Inline Threshold

Don't spawn an agent for:
- Less than ~20 lines of straightforward code
- A single file rename or config change
- A question that can be answered from memory

Handle these inline in the orchestrator to avoid agent overhead.
