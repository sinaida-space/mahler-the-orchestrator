---
name: mahler
description: Multi-model orchestrator that maximizes output quality at minimum token cost. Interrogates the user first, then uses Fable as creative director to decompose tasks and route them to Opus (complex), Sonnet (standard), or Haiku (mechanical). Use when the user types /mahler, says "orchestrate", "pipeline", "use mahler", or when facing a complex multi-step project that would benefit from task decomposition and model routing.
---

# Mahler Orchestrator Skill

This skill transforms the current agent into an orchestrator that decomposes work and routes subtasks to the optimal Claude model.

## When to Use

- Complex projects with multiple components (web app + shaders + config)
- Creative projects that need both conceptual direction and technical execution
- Any task where token efficiency matters and work can be parallelized
- When the user explicitly invokes /mahler

## How It Works

The orchestrator follows a strict 5-phase protocol defined in the `/mahler` command. Read `${CLAUDE_PLUGIN_ROOT}/commands/mahler.md` for the full protocol.

Quick reference:
1. **Phase 0 — Interrogate** — Six Hats multi-perspective questions, prompt quality audit, GitHub setup (Blue Hat)
2. **Phase 1 — Direct** — spawn Fable for creative direction + task decomposition into issue-ready specs
3. **Phase 2 — PRD Approval** — present the plan as a PRD in chat, hard stop until the user explicitly approves
4. **Phase 3 — GitHub Pipeline** — scouts → spec in issue body → dispatch by pointer → verifier → escalation ladder
5. **Phase 4 — Integrate** — dedicated Reviewer agent (fresh context) reads the full merged diff for correctness bugs, resource leaks, and cross-task conflicts; verify success criteria; surface summary

## Model Routing Quick Reference

Read `${CLAUDE_PLUGIN_ROOT}/skills/mahler/references/model-routing.md` for the detailed routing table.

## Key Principles

- **Interrogate before you orchestrate**: never skip Phase 0
- **Never dispatch before approval**: no issue is created and no agent is spawned until the user approves the Phase 2 PRD
- **Cheapest capable model wins**: don't use Opus for Haiku work
- **Parallel when possible**: independent tasks run simultaneously
- **Degrade gracefully**: if model switching is unavailable, the workflow structure still helps
