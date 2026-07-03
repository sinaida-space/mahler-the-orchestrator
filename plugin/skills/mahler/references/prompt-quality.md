# Prompt Quality — Anthropic Best Practices

Two uses: (1) Phase 0 checklist to assess the user's original request, (2) templates for writing subagent dispatch prompts.

---

## Phase 0: Evaluating the Original Request

Run this quick check on the user's original message before writing interrogation questions. Gaps here become wasted subagent tokens.

**Golden rule test:** Could a competent colleague with no context on this project follow this request without confusion? If not, it needs clarification.

| Gap | What to ask |
|-----|-------------|
| Vague output | "What exactly should this produce — a working feature, a prototype, a design doc?" |
| Missing success criteria | "How will you know this is done? What does 'good' look like vs. 'acceptable'?" |
| No context/motivation | "Why is this needed? What breaks or gets enabled?" |
| Unclear action vs. exploration | "Do you want me to implement this now, or explore options first?" |
| Format unspecified | "Any requirements on output format — file structure, naming, API shape?" |
| Scope ambiguous | "What's explicitly out of scope for this task?" |

**Action vs. suggestion test:** Current Claude models are literal. "Can you suggest changes to X?" → Claude will suggest, not implement. If the user clearly wants implementation, reframe in the spec: "Change X to do Y" not "Could you look at X." Propagate this into issue specs and dispatch prompts.

---

## Subagent Dispatch — Prompt Templates

### Universal opening (all subagents)

```
You are the <role>. Working directory: <absolute-path>.
<role-sentence: one line that focuses behavior and tone>
```

Role sentence examples:
- Implementer: "You implement exactly what the spec says — nothing more."
- Scout: "You gather facts and return them verbatim — you do not recommend or decide."
- Verifier: "You run the DoD check and report what you observe — you do not review code."

---

### Anti-hallucination block (scouts and any agent reading code)

```xml
<investigate_before_answering>
Never speculate about code you have not read. If the task references a specific file,
read it before answering. Investigate relevant files BEFORE making any claims about
the codebase. Give grounded, hallucination-free answers only.
</investigate_before_answering>
```

---

### Action default for implementers

```xml
<default_to_action>
Implement changes rather than suggesting them. If the user's intent is unclear, infer
the most useful action and proceed — use tools to discover missing details rather than
guessing. Do not ask clarifying questions; resolve ambiguity by reading the spec.
</default_to_action>
```

---

### Conservative default for scouts and verifiers

```xml
<do_not_act_before_instructions>
Do not modify files or take action unless the spec explicitly says to. Default to
reading, measuring, and reporting. Only proceed with edits when the spec requires it.
</do_not_act_before_instructions>
```

---

### Parallel tool calls (implementers and scouts)

```xml
<use_parallel_tool_calls>
If you intend to call multiple tools and there are no dependencies between them, call
all independent tools in parallel. When reading multiple files, read them simultaneously.
Maximize parallelism. Only call tools sequentially when a later call depends on the
result of an earlier one. Never use placeholder values for unknown parameters.
</use_parallel_tool_calls>
```

---

### Anti-overengineering (implementers)

```xml
<scope_discipline>
Only make changes the spec asks for. Do not refactor surrounding code, add docstrings
to code you didn't change, add error handling for impossible scenarios, create helpers
for one-time operations, or design for hypothetical future requirements.
The right complexity is the minimum needed for the current task.
</scope_discipline>
```

---

### General solution (implementers writing code)

```xml
<no_hardcoding>
Write a general-purpose solution, not one that passes only the test cases. Do not
hard-code values or create workarounds for specific inputs. Implement the actual logic.
If a test or requirement is wrong, say so rather than working around it.
</no_hardcoding>
```

---

### Context window continuity (long-running agents)

```xml
<context_continuity>
Your context window will be automatically compacted as it approaches its limit, allowing
you to continue working. Do not stop tasks early due to token budget concerns. As you
approach your limit, save current progress and state to your report file before the
context refreshes. Be persistent and complete tasks fully.
</context_continuity>
```

---

### Self-check (implementers)

Add at the end of any implementer prompt:

```
Before finishing: run the DoD verification from the spec. If any check fails, fix it
before reporting. Do not report success unless the verification command passes.
```

---

### Reversibility gate (for agents that might touch shared state)

```xml
<reversibility>
Before taking any action that is hard to reverse or affects shared systems, pause and
describe what you are about to do and why. Actions requiring confirmation: deleting
files, force-pushing, dropping databases, modifying CI/CD, posting to external services.
For local, reversible actions (edit files, run tests), proceed without asking.
</reversibility>
```

---

### XML structure for complex prompts

When a subagent prompt mixes several types of content, wrap each type:

```xml
<instructions>
  Numbered steps for what to do.
</instructions>

<context>
  Codebase facts, file locations, traps to avoid.
</context>

<constraints>
  What NOT to do.
</constraints>

<verification>
  Exact command to run and expected output.
</verification>
```

Use consistent tag names across all agent prompts in the session.

---

## Choosing Which Blocks to Include

| Agent type | Include |
|------------|---------|
| Scout | `<investigate_before_answering>`, `<do_not_act_before_instructions>`, `<use_parallel_tool_calls>` |
| Implementer | `<default_to_action>`, `<scope_discipline>`, `<no_hardcoding>`, `<use_parallel_tool_calls>`, `<context_continuity>`, self-check, reversibility gate |
| Verifier | `<do_not_act_before_instructions>`, self-check |
| Creative Director (Fable) | None of the above — Fable works from interrogation answers, not templated blocks |

---

## Key Principles Summary (from Anthropic docs)

- **Be explicit about actions.** "Change X" → implements. "Can you look at X" → suggests only.
- **Add motivation.** "Never use ellipses" is weaker than "Never use ellipses — this is read aloud by TTS."
- **Tell Claude what TO do, not what not to do.** "Write in flowing prose paragraphs" beats "don't use bullet points."
- **Put long context at the top.** Data and documents above the query; query at the end (up to 30% quality improvement in long-context tasks).
- **Match prompt style to desired output.** Heavy markdown in the prompt → more markdown in output.
- **3–5 diverse examples** for consistent output formatting — wrap in `<example>` tags.
- **Prefer general instructions over prescriptive steps.** "Think thoroughly" often beats a hand-written step plan. Claude's reasoning usually exceeds what a human would prescribe.
- **Ask for self-check.** "Before finishing, verify your answer against [criteria]" catches errors reliably.
