![Mahler: The Orchestrator](assets/mahler-header.png)

# Mahler: The Orchestrator

A multi-model Claude plugin that routes your creative and technical work to the AI model that fits each task.

---

## Why Mahler?

**Gustav Mahler** conducted orchestras by bringing out the best in each musician. He didn't ask the violins to play tuba parts. This plugin does the same thing with Claude's model ensemble.

The name is also a small tribute to my ballet life. I love the *Adagio* from Mahler's 5th Symphony. That piece is about finding beauty in complexity, which feels right for this tool.

---

## The problem it solves

You're on a limited token budget (Pro plan, anyone?). You have a complex project: maybe a shader + a web app + some interactive TouchDesigner piece. If you throw everything at Claude's most powerful model, you burn tokens on tasks that don't need that power.

**Mahler solves this by:**
- Asking the right questions *first* (fewer tokens spent on wrong directions)
- Choosing the simplest architecture that fits, so a one-call task never becomes a three-agent pipeline
- Breaking your project into subtasks, only as far as that architecture needs
- Routing each task to the model it needs
- Handling complex work (shaders, architecture) with Opus
- Handling standard stuff (CSS, tests, docs) with Sonnet
- Handling mechanical work (formatting, boilerplate) with Haiku
- Orchestrating everything with the strongest model your plan offers (the chat's own model and effort) for planning

Result: each task runs on the model suited to it.

---

## How it works: five phases

### Phase 0: Interrogation
Before writing a single line of code, Mahler asks you pointed questions through an interactive choice dialogue:
- What exactly are we building?
- What's out of scope?
- What constraints matter? (platform, performance, aesthetic)
- How do we know it's done?
- Are there decision forks to resolve?

This phase keeps the work from starting in the wrong direction.

### Phase 1: Planning
The Planner synthesizes your answers and produces:
- A creative/technical vision
- **An architecture pattern for the task**: a single model call, a reflection loop, a fixed chain, a multi-agent split, or a knowledge-graph-backed setup. Mahler picks the cheapest one that fits and moves up only for a named reason. Most tasks are a single call or a short chain, and saying so stops an agent fleet spawning by reflex.
- A decomposition plan, only as deep as the pattern needs
- Model and effort routing for each task

The Planner runs on the strongest model your subscription can spawn, picked at run time: Opus → Sonnet (high effort) → Mahler itself. On Pro that means Opus (currently Opus 5.5). Model aliases resolve to the newest release of each family, so a new Opus is picked up without any change to Mahler.

### Phase 2: PRD approval
Mahler turns the plan into a short spec with scope, architecture, task table, token estimate and execution mode, and then stops. Nothing is built and no agent is spawned until you approve it.

### Phase 3: Execution
Tasks run through a GitHub-issue-first pipeline, each on the right model:
- **Opus** → Shaders, complex algorithms, architecture decisions, hard debugging
- **Sonnet** → Web features, tests, docs, API integration, standard implementation
- **Haiku** → File operations, formatting, boilerplate, config changes

Each agent gets a focused brief and has to earn its spawn overhead: work smaller than a spawn is done inline, adjacent tasks merge, and one verifier covers a group rather than one per task.

### Phase 4: Integration
A fresh-context reviewer runs the project's own checks (build, lint, tests) against the merged result, then reads the full diff for cross-task conflicts. Findings become fix commits. Then Mahler verifies everything against your Phase 0 success criteria. Mahler keeps a person in charge by design. You approve the plan before any agent starts, and the review checks the whole result against your own criteria.

---

## Choosing an architecture

Mahler treats “how many agents” as the wrong question. Before decomposing anything, it names the shape the task actually needs:

| Task shape | Pattern |
|---|---|
| One well-scoped output, checkable in one pass | single model call |
| Output needs revising against a rubric | reflection loop |
| A fixed sequence of narrow transforms | chain, with a gate between each step |
| Genuinely independent concerns | multi-agent, one per file-disjoint group |
| The same facts are read across sessions or by several tasks | knowledge graph |

It starts at the cheapest pattern and moves up only when a specific failure demands it.

**Knowledge graphs.** Mahler does not carry a graph itself, but when a job needs to remember entities and relationships across sessions, it can scaffold one as part of the plan: a typed JSON or SQLite store (not Neo4j, for the scale these projects hit), with provenance on every edge and additive writes. This lands as its own approved task in the plan, with the entities and edges it will hold spelled out.

---

## Installation

### Quick start

1. **Copy the plugin** to your Claude Code installation:
   ```bash
   git clone https://github.com/sinaida-space/mahler-the-orchestrator.git
   cp -r mahler-the-orchestrator ~/.claude/skills/mahler
   ```

2. **Start a new Claude Code session** (reload to register the command)

3. **Type `/mahler`** to launch the orchestrator

### What you get

- **`/mahler` command** → Launches the full 5-phase workflow
- **`/mahler` skill** → Provides routing logic and interrogation templates
- **Four agent roles** → Planner (strongest available model), Senior Developer (Opus), Developer (Sonnet), Assistant (Haiku)

---

## Example: building an interactive shader web app

You want to create an interactive web app that displays real-time shader animations. Here's how Mahler handles it:

### Phase 0: Interrogation
*Mahler asks:*
- Target: desktop, mobile, or both?
- Performance: 60fps? 30fps?
- Shader language: GLSL? Custom uniforms?
- What data does it visualize?
- Should it respond to user input (mouse, touch, audio)?

### Phase 1: Planning
*The Planner suggests:*
- Visual direction: “Generative light patterns responding to input”
- Architecture: multi-agent, since the shader math, the React wrapper, and the animation loop are genuinely independent work on different files
- Decomposition:
  - Task 1 (Sonnet): React component + WebGL canvas setup
  - Task 2 (Opus): Fragment shader with math-heavy uniforms
  - Task 3 (Sonnet): Animation loop + performance optimization
  - Task 4 (Haiku, inline): Config file + boilerplate, smaller than a spawn and folded into Task 1

### Phase 2: PRD approval
Mahler shows the scope, the architecture call, the task table with token estimates, and waits for your yes.

### Phase 3: Execution
Three agents work in parallel:
- Sonnet builds the React wrapper (and the config)
- Opus writes the shader
- Sonnet does the animation loop

### Phase 4: Integration
Run the build and tests against the merged result, review the full diff, test performance, iterate if needed.

**Result:** A polished interactive app, with each part built on the model suited to it.

---

## Model routing reference

**Haiku** (cheapest, use by default):
- File operations, formatting, boilerplate generation
- Package.json changes, config files
- Simple refactors, renaming, cleanup
- Copying patterns

**Sonnet** (standard, handles most work):
- Web development (React, JS, CSS, HTML)
- API integration, standard features
- Tests and documentation
- TouchDesigner Python scripts
- Component architecture

**Opus** (expensive, use only when needed):
- GLSL shader development and math
- Complex algorithms and system design
- Hard debugging and performance optimization
- Architecture decisions
- Novel problem-solving

---

## For your creative practice

If you work with interactive projections, TouchDesigner, shaders, and web, this tool is built for you. Mahler understands:
- Real-time performance constraints (60fps is non-negotiable)
- The interplay between art and technology
- When to prioritize visual polish vs. technical elegance
- Your creative decisions matter as much as the code

---

## Limitations

- Mahler requires Claude Code, the CLI or an IDE extension.
- Model switching needs Claude Pro, Max or the API. With a single model, Mahler keeps the same workflow.
- The Planner uses the chat's own model and effort, and falls back to Opus → Sonnet → Mahler itself only if that is rejected.
- The prose check in review uses the [typography skill](https://github.com/sinaida-space/tardis_type) when it is installed, and is skipped without it.
- Mahler routes the work, and your creative decisions drive the direction.

---

## What's Included

- **Installation guide** → `docs/INSTALLATION.md`
- **Model routing decision tree** → `docs/ROUTING.md`
- **Interrogation protocol** → `docs/INTERROGATION.md`
- **Example workflows** → `examples/`

---

## Questions?

If Mahler doesn't work as expected or you have ideas for improvement, [open an issue](https://github.com/sinaida-space/mahler-the-orchestrator/issues).

---

## About

Designed by **Sinaida Krivchenko**, a new media artist working with interactive projections, TouchDesigner and AI agents.

- [Website](https://sinaida.eu/)
- [Instagram](https://www.instagram.com/sin.ai.da/)
- A small tribute to Gustav Mahler and the ballet dancers who taught me to see rhythm in complexity

---

**Ready to orchestrate?** Start your Claude Code session and type `/mahler`.

## License

Apache 2.0, see [LICENSE](LICENSE) and [NOTICE](NOTICE).

© 2026 Sinaida Krivchenko · [sinaida.eu](https://sinaida.eu)

This repo is a production mirror.

I develop in a private repo and publish releases here. Issues and ideas are welcome: I read them and carry them over. Pull requests stay open as suggestions; I carry good changes over by hand and then close the PR.

<!-- Je suis le spectre d'une rose que tu portais hier au bal. Théophile Gautier, 1837 -->
