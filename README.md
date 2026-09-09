![Mahler: The Orchestrator](assets/mahler-header.png)

# Mahler: The Orchestrator

A multi-model Claude plugin that routes your creative and technical work to the optimal AI model—saving tokens while maximizing quality.

---

## Why Mahler?

**Gustav Mahler** conducted orchestras by bringing out the best in each musician. He didn't ask the violins to play tuba parts. This plugin does the same thing with Claude's model ensemble.

The name is also a small tribute to my ballet life—I love the *Adagio* from Mahler's 5th Symphony. That piece is about finding beauty in complexity, which feels right for this tool.

---

## The Problem It Solves

You're on a limited token budget (Pro plan, anyone?). You have a complex project: maybe a shader + a web app + some interactive TouchDesigner piece. If you throw everything at Claude's most powerful model, you burn tokens on tasks that don't need that power.

**Mahler solves this by:**
- Asking the right questions *first* (no wasted tokens on wrong directions)
- Choosing the simplest architecture that fits, so a one-call task never becomes a three-agent pipeline
- Breaking your project into subtasks, only as far as that architecture needs
- Routing each task to the cheapest model that can handle it
- Handling complex work (shaders, architecture) with Opus
- Handling standard stuff (CSS, tests, docs) with Sonnet
- Handling mechanical work (formatting, boilerplate) with Haiku
- Orchestrating everything with Fable for creative direction

Result: same quality, way fewer tokens burned.

---

## How It Works: Five Phases

### Phase 0: Interrogation
Before writing a single line of code, Mahler asks you pointed questions through an
interactive choice dialogue:
- What exactly are we building?
- What's out of scope?
- What constraints matter? (platform, performance, aesthetic)
- How do we know it's done?
- Are there decision forks to resolve?
- What's your token budget for this?

This phase prevents the biggest token waste: building the wrong thing.

### Phase 1: Creative Direction
Fable (Claude's most creative, lightweight model) synthesizes your answers and produces:
- A creative/technical vision
- **An architecture pattern for the task** — a single model call, a reflection loop, a
  fixed chain, a multi-agent split, or a knowledge-graph-backed setup. Mahler picks the
  cheapest one that fits and moves up only for a named reason. Most tasks are a single
  call or a short chain, and saying so stops an agent fleet spawning by reflex.
- A decomposition plan, only as deep as the pattern needs
- Model and effort routing for each task

If Fable isn't available on your plan, Mahler handles this phase itself using the same logic.

### Phase 2: PRD approval
Mahler turns the plan into a short PRD — scope, architecture, task table, token
estimate, execution mode — and stops. Nothing is built and no agent is spawned until
you approve it.

### Phase 3: Execution
Tasks run through a GitHub-issue-first pipeline, each on the right model:
- **Opus** → Shaders, complex algorithms, architecture decisions, hard debugging
- **Sonnet** → Web features, tests, docs, API integration, standard implementation
- **Haiku** → File operations, formatting, boilerplate, config changes

Each agent gets a focused brief and has to earn its spawn overhead: work smaller than a
spawn is done inline, adjacent tasks merge, and one verifier covers a group rather than
one per task.

### Phase 4: Integration
A fresh-context reviewer runs the project's own checks (build, lint, tests) against the
merged result, then reads the full diff for cross-task conflicts. Findings become fix
commits. Then Mahler verifies everything against your Phase 0 success criteria.

---

## Choosing an architecture

Mahler treats "how many agents" as the wrong question. Before decomposing anything, it
names the shape the task actually needs:

| Task shape | Pattern |
|---|---|
| One well-scoped output, checkable in one pass | single model call |
| Output needs revising against a rubric | reflection loop |
| A fixed sequence of narrow transforms | chain, with a gate between each step |
| Genuinely independent concerns | multi-agent, one per file-disjoint group |
| The same facts are read across sessions or by several tasks | knowledge graph |

It starts at the cheapest pattern and moves up only when a specific failure demands it.

**Knowledge graphs.** Mahler does not carry a graph itself, but when a job needs to
remember entities and relationships across sessions, it can scaffold one as part of the
plan: a typed JSON or SQLite store (not Neo4j, for the scale these projects hit), with
provenance on every edge and additive writes. This lands as its own approved task in the
PRD, with the entities and edges it will hold spelled out.

---

## Installation

### Quick Start

1. **Copy the plugin** to your Claude Code installation:
   ```bash
   git clone https://github.com/sinaida-space/mahler-the-orchestrator.git
   cp -r mahler-the-orchestrator ~/.claude/skills/mahler
   ```

2. **Start a new Claude Code session** (reload to register the command)

3. **Type `/mahler`** to launch the orchestrator

### What You Get

- **`/mahler` command** → Launches the full 5-phase workflow
- **`/mahler` skill** → Provides routing logic and interrogation templates
- **Four agent roles** → Creative Director (Fable), Senior Developer (Opus), Developer (Sonnet), Assistant (Haiku)
- **Global CLAUDE.md** → Sets up context about your practice and working style

---

## Example: Building an Interactive Shader Web App

You want to create an interactive web app that displays real-time shader animations. Here's how Mahler handles it:

### Phase 0: Interrogation
*Mahler asks:*
- Target: desktop, mobile, or both?
- Performance: 60fps? 30fps?
- Shader language: GLSL? Custom uniforms?
- What data does it visualize?
- Should it respond to user input (mouse, touch, audio)?

### Phase 1: Creative Direction
*Fable suggests:*
- Visual direction: "Generative light patterns responding to input"
- Architecture: multi-agent — the shader math, the React wrapper, and the animation loop
  are genuinely independent work on different files
- Decomposition:
  - Task 1 (Sonnet): React component + WebGL canvas setup
  - Task 2 (Opus): Fragment shader with math-heavy uniforms
  - Task 3 (Sonnet): Animation loop + performance optimization
  - Task 4 (Haiku, inline): Config file + boilerplate — smaller than a spawn, folded into Task 1

### Phase 2: PRD approval
Mahler shows the scope, the architecture call, the task table with token estimates, and
waits for your yes.

### Phase 3: Execution
Three agents work in parallel:
- Sonnet builds the React wrapper (and the config)
- Opus writes the shader
- Sonnet does the animation loop

### Phase 4: Integration
Run the build and tests against the merged result, review the full diff, test
performance, iterate if needed.

**Result:** A polished interactive app, optimized token spend, no wasted effort on wrong directions.

---

## Token Savings: Real Numbers

**Without Mahler** (linear work on Opus):
- Interrogation + planning: 500k tokens
- Shader work: 800k tokens
- Web app work: 600k tokens
- Config/boilerplate: 200k tokens
- **Total: ~2.1M tokens**

**With Mahler** (routed work):
- Interrogation (current model): 100k tokens
- Fable direction: 150k tokens
- Opus shader: 400k tokens
- Sonnet web: 350k tokens
- Haiku boilerplate: 50k tokens
- **Total: ~1.05M tokens** (50% savings)

---

## Model Routing Reference

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

## For Your Creative Practice

If you work with interactive projections, TouchDesigner, shaders, and web—this tool is built for you. Mahler understands:
- Real-time performance constraints (60fps is non-negotiable)
- The interplay between art and technology
- When to prioritize visual polish vs. technical elegance
- Your creative direction matters as much as the code

---

## Limitations

- **Requires Claude Code** (the CLI or IDE extension)
- **Model switching** needs Claude Pro/Max (or API). On Pro plan? Mahler degrades gracefully—same workflow, one model
- **Fable availability** varies by plan. If unavailable, Mahler performs creative direction itself
- **Not a replacement for you** — Mahler routes work, but your creative decisions drive the direction

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

Built by **Sinaida Krivchenko** — a new media artist working with interactive projections, TouchDesigner, shaders, and web applications.

- 🌐 [Website](https://sinaida.eu/)
- 📸 [Instagram](https://www.instagram.com/sin.ai.da/)
- 🎵 A small tribute to Gustav Mahler and the ballet dancers who taught me to see rhythm in complexity

---

**Ready to orchestrate?** Start your Claude Code session and type `/mahler`.

## License

Apache 2.0, see [LICENSE](LICENSE) and [NOTICE](NOTICE).

© 2026 Sinaida Krivchenko · [sinaida.eu](https://sinaida.eu)
