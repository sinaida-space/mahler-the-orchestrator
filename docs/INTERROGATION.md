# The Interrogation Protocol (Phase 0)

Before Mahler decomposes your project, it interrogates you. This is the most important phase.

Why? Because every token spent here prevents 10x waste downstream.

## The Goal

By the end of Phase 0, you should know:
1. **What** you're building (in concrete terms)
2. **What** you're NOT building (out of scope)
3. **How** you'll know it's done (success criteria)
4. **What constraints** matter (technical, creative, performance)
5. **What already exists** that you're building on

## Question Categories

### Scope Definition
- "What exactly should this produce? A working prototype? A finished app? A proof of concept?"
- "What's in scope? What's explicitly out of scope?"
- "Is this new, or are we extending something existing?"
- "Who's the audience?"

### Technical Constraints
- "What platform/runtime? Browser? Node? TouchDesigner? Standalone?"
- "Any specific frameworks or libraries? Or should I choose?"
- "Performance: 60fps realtime? 30fps? Batch processing?"
- "Target devices? Desktop? Mobile? Projection hardware?"
- "Size/scale: 1 user? 1000 users? Single file?"

### Creative Direction (for art-adjacent work)
- "What's the visual/conceptual feel?"
- "Any references or inspiration?"
- "Generative/procedural, or hand-crafted?"
- "What's the audience experience?"
- "Color, mood, texture preferences?"

### Success Criteria
- "How will we know this is done?"
- "What's 'good enough' vs. 'perfect'?"
- "Any specific deliverables (files, deployment, docs)?"
- "What would make this fail?"

### Dependencies
- "What exists already that I should build on?"
- "Files I should read first?"
- "External APIs, data, or hardware involved?"

## Decision Forks

When the user's answer implies multiple valid paths, surface the fork:

> "I see a decision point: we could **[A]** or **[B]**. Option A means [tradeoff]. Option B means [tradeoff]. Which direction?"

**Common forks:**
- Build from scratch vs. extend existing code
- Performance-first vs. readability-first
- Minimal viable vs. polished
- Single file vs. modular architecture
- Client-side vs. server-side processing

## When to Stop Asking

Stop when you can answer **all five** of these:
1. I know what to build ✓
2. I know what NOT to build ✓
3. I know the technical constraints ✓
4. I know how we'll verify it works ✓
5. There are no unresolved forks ✓

If you can't answer one, keep asking.

## Example: Interrogating a Shader Project

**User:** "I want to make a shader that responds to music."

**Mahler asks:**
1. "What does 'respond to music' mean? Frequency analysis? Beat detection? Volume sensitivity?"
2. "What platform? GLSL in a web browser? TouchDesigner? Standalone?"
3. "Visual output: abstract colors? Geometric shapes? Particles?"
4. "Real-time 60fps or lower frame rate OK?"
5. "Should it be interactive (user can control parameters), or just playback?"
6. "Existing shader code to build on, or start from scratch?"

**User answers reveal the real project:**
- Fragment shader + Web Audio API + React wrapper
- Target: interactive web app + projection mapping
- Performance: 60fps critical
- Starting fresh

**Now Mahler can decompose with confidence** (not guessing).

## Pro Tips

1. **Be specific.** "Fast" means different things. "60fps real-time" is clear.
2. **Surface assumptions.** If you assume something, say it. "I'm assuming you want this in the browser—correct?"
3. **Dig into the "why."** "Why is 60fps important? Is it for human perception or specific hardware?"
4. **Listen for vagueness.** If the user is vague, they haven't decided yet. Surface the fork.

---

## Common Mistakes to Avoid

❌ **Skipping Phase 0** — "Let's just start building"
→ Result: wrong direction, wasted tokens

❌ **Asking too many questions at once**
→ Result: user overwhelmed, half-baked answers

❌ **Not surfacing decision forks**
→ Result: Mahler guesses, guess is wrong

❌ **Not verifying constraints**
→ Result: "I didn't know you needed 60fps" after building at 30fps

✅ **Start narrow, expand** — Get the core use case crystal clear before asking nice-to-haves

✅ **Reflect back** — "So you want X, not Y, with Z constraint. Correct?"

✅ **Stop when confident** — You don't need perfect answers, just clear ones
