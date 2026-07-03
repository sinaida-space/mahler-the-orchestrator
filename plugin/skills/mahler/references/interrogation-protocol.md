# Interrogation Protocol

Phase 0 is the most important phase. Every token spent here saves 10x in Phase 2.

## Question Categories

### 1. Scope Definition
- "What exactly should this produce? A working app, a prototype, a proof of concept?"
- "What's explicitly out of scope?"
- "Is this a new project or extending something existing?"

### 2. Technical Constraints
- "What platform/runtime? (browser, Node, TouchDesigner, standalone)"
- "Any framework or library requirements? Or should I choose?"
- "Performance requirements? (realtime 60fps, interactive, batch processing)"
- "Target devices? (desktop, mobile, projection hardware)"

### 3. Creative Direction (for art-adjacent tasks)
- "What's the visual feel you're going for? Any references?"
- "Is this generative/procedural or hand-crafted?"
- "What's the audience experience? (passive viewing, interactive, immersive)"
- "Color palette, mood, texture preferences?"

### 4. Success Criteria
- "How will we know this is done?"
- "What does 'good enough' look like vs. 'perfect'?"
- "Are there specific deliverables (files, deployment, documentation)?"

### 5. Dependencies & Context
- "What already exists that I should build on?"
- "Are there files I should read first?"
- "Any external APIs, data sources, or hardware involved?"

## Fork Detection

When the user's answer implies multiple valid paths, surface the fork:

"I see a decision point: we could [A] or [B]. Path A means [tradeoff]. Path B means [tradeoff]. Which direction?"

Common forks:
- Build from scratch vs. extend existing code
- Performance-first vs. readability-first
- Minimal viable vs. polished
- Single file vs. modular architecture
- Client-side vs. server-side processing

## When to Stop Asking

Stop when you can answer ALL of these:
1. I know what to build
2. I know what NOT to build
3. I know the technical constraints
4. I know how we'll verify it works
5. There are no unresolved forks

If you can't answer all five, keep asking.
