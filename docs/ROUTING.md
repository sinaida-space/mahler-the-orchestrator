# Model Routing Decision Tree

When Mahler decomposes your project into subtasks, each task gets assigned a model. Use this guide to understand *why* each task goes where.

## Quick Decision Tree

```
Is this mechanical? (formatting, config, boilerplate, file ops)
  → YES → haiku (cheapest)
  → NO ↓

Is this standard implementation? (web features, tests, docs, API)
  → YES → sonnet (standard)
  → NO ↓

Is this complex? (shaders, architecture, algorithms, hard debugging)
  → YES → opus (powerful)
  → NO → sonnet (default to standard)
```

## Haiku: Lightweight Assistant

**Use for:**
- File operations (create, rename, copy)
- Formatting (indentation, linting)
- Boilerplate generation
- Config files (package.json, tsconfig.json)
- Simple refactors and cleanup
- Copying patterns

**Why:**
- Cheapest model
- Perfect for predictable, mechanical tasks
- Fast execution
- No creativity needed

**Examples:**
- Generate a `.gitignore`
- Rename variables across a file
- Add a missing import
- Format code

---

## Sonnet: Standard Developer

**Use for:**
- Web development (React, Vue, JS, CSS, HTML)
- API integration and REST endpoints
- Writing tests (unit, integration)
- Documentation and README files
- TouchDesigner Python scripts
- Standard component architecture
- Debugging common issues

**Why:**
- Balanced: smart enough for complex features, efficient on tokens
- Understands web patterns and conventions
- Good at explaining technical concepts
- Reliable for most day-to-day work

**Examples:**
- Build a React component
- Write unit tests
- Create API endpoints
- Document a shader parameter
- Build a state management system

---

## Opus: Senior Developer

**Use for:**
- GLSL shader development (vertex, fragment, compute)
- Complex shader math (transformations, lighting, effects)
- System architecture and design decisions
- Performance optimization strategies
- Hard debugging (race conditions, memory leaks)
- Algorithm design (compression, encoding, rendering)
- Novel problem-solving

**Why:**
- Most powerful model
- Excels at mathematical reasoning
- Handles deep technical problems
- Necessary for cutting-edge work

**Examples:**
- Write a complex fragment shader with custom math
- Optimize a rendering pipeline
- Debug a mysterious performance issue
- Design a system for handling 10k+ particles in real-time
- Implement a procedural texture generation algorithm

---

## Real Project Example: Interactive Projection

```
Project: Real-time audio-responsive shader projection

Task 1: React component wrapper for WebGL canvas
  → Model: sonnet (standard web pattern)

Task 2: Fragment shader with audio input uniforms
  → Model: opus (math-heavy, visual complexity)

Task 3: Audio input preprocessing (Web Audio API)
  → Model: sonnet (standard, known patterns)

Task 4: Package.json configuration
  → Model: haiku (mechanical)

Task 5: README and documentation
  → Model: haiku (mechanical) or sonnet (if needs explanation)
```

---

## The Prime Directive

**Always assign the cheapest model that can *reliably complete* the task.**

- Don't use Opus for CSS styling (Sonnet handles it)
- Don't use Sonnet for config file generation (Haiku handles it)
- When in doubt between two tiers, choose cheaper — escalate only on failure

---

## Escalation

If an agent returns an error or incomplete result:
- Haiku task failing? → Re-run with Sonnet
- Sonnet task hitting a wall? → Escalate to Opus
- Fable unavailable? → Orchestrator handles creative direction itself

---

## Token Cost Estimate

Assuming 1M token budget:

| Allocation | Tokens | Best For |
|-----------|--------|----------|
| Haiku (40%) | 400k | Mechanical work, cleanup |
| Sonnet (40%) | 400k | Standard features, tests |
| Opus (20%) | 200k | Complex/novel work |

This is a starting point. Your real allocation depends on your project.
