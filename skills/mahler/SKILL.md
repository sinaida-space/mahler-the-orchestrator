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
1. **Phase 0 — Interrogate** — Six Hats multi-perspective questions via the `AskUserQuestion` dialogue tool (never plain text), prompt quality audit, GitHub setup (Blue Hat)
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
- **Budget-aware execution**: Phase 0 asks about remaining budget; the PRD states an execution mode (Full Orchestra / Chamber / Solo) the user approves — on a tight budget the orchestrator implements the PRD itself instead of spawning a fleet (see `references/execution-modes.md`)
- **Availability over assumption**: the model hierarchy is a fallback ladder, not a requirement — fable→self, opus→sonnet-high, haiku→sonnet-low, nothing→Solo
- **Degrade gracefully**: the 5-phase structure, PRD stop, and issue-first survive every mode; only the number of agents changes
