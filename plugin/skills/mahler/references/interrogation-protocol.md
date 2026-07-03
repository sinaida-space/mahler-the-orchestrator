# Interrogation Protocol

Phase 0 is the most important phase. Every token spent here saves 10x in Phase 2.

## Pre-Check: Prompt Quality Audit

Before writing Six Hats questions, run a 30-second audit of the original request.
Read `${CLAUDE_PLUGIN_ROOT}/skills/mahler/references/prompt-quality.md` for the full checklist.

Quick gaps to catch:

| If the request… | Then ask… |
|-----------------|-----------|
| Says "can you suggest / look at / consider" | "Do you want me to implement this, or explore options first?" |
| Has no success criteria | "How will you know this is done? What does the result look like?" |
| Has no stated motivation | "Why is this needed — what breaks or gets enabled?" |
| Scope is unclear | "What's explicitly out of scope?" |
| Output format unspecified | "Any constraints on file structure, API shape, or naming?" |

**Golden rule:** Could a competent colleague with no prior context follow this request without confusion? If not, that confusion becomes a wasted subagent run.

## Six Hats Framework

Use de Bono's Six Thinking Hats to generate questions across all creative and technical perspectives. The hats ensure you don't miss angles that are obvious in retrospect.

### ⚪ White Hat — Facts & Information
Questions that establish ground truth:
- "What already exists? Which files/components do I build on?"
- "What platform/runtime? (browser, Node, TouchDesigner, embedded, projection hardware)"
- "Any framework, library, or API requirements?"
- "Performance requirements? (realtime 60fps, interactive latency, batch)"
- "Target devices or display format?"

### 🔴 Red Hat — Intuition & Feeling
Questions that surface the emotional and aesthetic intent:
- "What's the visceral feel you're going for? A single adjective?"
- "Any visual or conceptual references? (artworks, artists, textures, moods)"
- "What should this make the audience feel?"
- "What would make you proud vs. just satisfied?"

### ⚫ Black Hat — Risk & Critical Judgment
Questions that find the hard edges:
- "What's the single most likely thing to fail or blow up scope?"
- "Anything explicitly NOT allowed? (third-party services, languages, approaches)"
- "What's the shortest path to something embarrassing or broken?"
- "Are there known traps in the existing codebase?"

### 🟡 Yellow Hat — Value & Vision
Questions that clarify success:
- "What does 'done and great' look like? What can the user do that they couldn't before?"
- "Who is the audience? How close will they be to this?"
- "What's the minimal version worth shipping vs. the full vision?"
- "How will we know this is done? Specific test or observable behavior?"

### 🟢 Green Hat — Alternatives & Creative Forks
Questions that surface unexplored paths:
- "Have you considered [alternative approach]? Why or why not?"
- "Is there a simpler version of this that accomplishes 80% of the value?"
- "Should this be generative/procedural or hand-crafted?"
- "Any wild idea you had that seemed impractical but stuck with you?"

### 🔵 Blue Hat — Process & Management
Questions about how the work is organized (always ask these):
- "Does this project have a GitHub repo? Should we initialize one?"
- "How do you want to version this? (feature branches, direct to main, tags at milestones)"
- "Any existing issue tracker or project board?"
- "Are there collaborators, or is this solo?"
- "Hard deadline or soft milestone?"

## Question Count

Ask **5–9 questions total** spanning the hats. Don't ask all six hats exhaustively — pick the 1–2 most important questions per hat for this specific task. Art-adjacent tasks get more Red/Green; engineering tasks get more White/Black.

## Fork Detection

When an answer implies multiple valid paths, surface the fork explicitly:

"I see a decision point: we could [A] or [B]. Path A means [tradeoff]. Path B means [tradeoff]. Which?"

Common forks:
- Build from scratch vs. extend existing code
- Performance-first vs. readability-first
- Minimal viable vs. polished
- Single file vs. modular architecture
- Client-side vs. server-side processing
- Generative vs. hand-crafted
- Realtime vs. pre-rendered

## When to Stop Asking

Stop when you can answer ALL of these:
1. I know what to build
2. I know what NOT to build
3. I know the technical constraints
4. I know how we'll verify it works
5. Zero unresolved forks

If you can't answer all five, keep asking — but reframe so you're not repeating yourself.

## Resolving Forks Yourself

If a fork is purely technical and doesn't change scope or cost, resolve it yourself in the spec and note the decision in "Resolved Forks." Only block on forks that change scope, timeline, or user experience.
