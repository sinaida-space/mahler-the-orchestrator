# Model Routing Table

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

## Escalation Rules

If a haiku agent returns an error or incomplete result, escalate to sonnet.
If a sonnet agent hits a wall on a subtask, escalate to opus.
Never escalate fable work — if fable is unavailable, the orchestrator handles it.

## Inline Threshold

Don't spawn an agent for:
- Less than ~20 lines of straightforward code
- A single file rename or config change
- A question that can be answered from memory

Handle these inline in the orchestrator to avoid agent overhead.
