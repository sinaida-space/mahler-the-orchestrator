# Example: Building an Interactive Shader Web App

Let's walk through a real project using `/mahler` from start to finish.

## The Brief

"I want to build an interactive web app that displays generative shader animations. Users can tweak parameters in real-time. Should work on desktop and tablet."

---

## Phase 0: Interrogation

**Mahler asks:**

1. "What kind of animations? Abstract patterns? Particles? Procedural landscape?"
   - **User:** "Abstract generative patterns, responding to parameter changes"

2. "Desktop and tablet—what about mobile (small phones)?"
   - **User:** "Optimize for tablet size and up, phone is nice-to-have"

3. "Real-time performance: 60fps target?"
   - **User:** "Yes, 60fps is critical"

4. "Interaction: mouse/touch to tweak parameters, or UI controls?"
   - **User:** "UI controls (sliders), and mouse drag to rotate the view"

5. "Any existing shader code to build on?"
   - **User:** "I have a fragment shader, but it needs to be converted to WebGL and needs new uniforms"

6. "How will we know it's done?"
   - **User:** "60fps, parameters update smoothly, looks visually interesting, deployed to a public URL"

**By now, Mahler understands:**
- ✓ What: Generative shader + web UI for parameter tweaking
- ✓ What not: Mobile optimization, server-side features, data persistence
- ✓ Success criteria: 60fps, smooth interaction, visual appeal, deployed
- ✓ Constraints: Desktop+tablet, real-time performance critical
- ✓ Dependencies: Existing fragment shader to adapt

---

## Phase 1: Creative Direction (Fable)

**Fable synthesizes and returns:**

```
## Vision
A meditative, interactive generative art piece. Users adjust parameters 
to explore variations of the pattern. Visual emphasis on color transitions 
and symmetry breaking.

## Execution Plan

### Task 1: React + WebGL Canvas Setup
- Model: sonnet
- Brief: Build a React component that hosts a WebGL canvas. 
  Include resize handling, device pixel ratio for retina displays.
  Add a control panel for parameter sliders (will receive values from Task 2).
- Files: src/components/ShaderViewer.tsx, src/App.tsx
- Depends on: nothing

### Task 2: Fragment Shader Adaptation
- Model: opus
- Brief: Adapt the provided fragment shader to WebGL GLSL.
  Add uniform inputs for: scale, rotation, hue_shift, complexity.
  Optimize for 60fps performance (watch for expensive operations).
- Files: src/shaders/fragment.glsl
- Depends on: Task 1 (needs to know uniform names)

### Task 3: Animation Loop + Parameter Binding
- Model: sonnet
- Brief: Connect the WebGL render loop to the React state.
  Update uniforms when sliders change. Add mouse interaction for view rotation.
  Implement performance monitoring (log actual fps).
- Files: src/hooks/useShaderRenderer.ts
- Depends on: Task 1, Task 2

### Task 4: UI Polish + Deployment Config
- Model: haiku
- Brief: Add styling for the control panel (dark theme, responsive).
  Create package.json with build scripts. Generate .gitignore, Dockerfile if needed.
- Files: src/styles/ControlPanel.css, package.json, .gitignore
- Depends on: Task 1

Tasks 1 & 4 can run in parallel (no dependencies).
```

---

## Phase 2: Execution

### Agent 1 (Sonnet): React + WebGL Canvas

Sonnet creates `src/components/ShaderViewer.tsx`:
```typescript
// Handles canvas setup, resize, device pixel ratio
// Exports hooks for shader renderer to use
```

And `src/App.tsx`:
```typescript
// Main app structure with SliderControls component
```

### Agent 2 (Opus): Fragment Shader

Opus creates `src/shaders/fragment.glsl`:
```glsl
// Adapted shader with uniforms:
// uniform float scale;
// uniform float rotation;
// uniform float hueShift;
// uniform float complexity;
// Optimized math, early returns for performance
```

### Agent 3 (Sonnet): Animation Loop

Sonnet creates `src/hooks/useShaderRenderer.ts`:
```typescript
// WebGL render loop
// Uniform updates on parameter change
// Mouse interaction (rotation)
// FPS monitoring
```

### Agent 4 (Haiku): Config + Styling

Haiku creates:
- `src/styles/ControlPanel.css`
- `package.json` with build scripts
- `.gitignore`, `Dockerfile` (if needed)

All agents report back with their output.

---

## Phase 3: Integration

You review:
- ✓ Canvas renders without black screen
- ✓ Parameters update smoothly
- ✓ FPS stays at 60 on desktop/tablet
- ✓ Visual output is interesting
- ✓ Code is clean, no console errors

Minor issue found: shader has a performance spike at high complexity values.

**Decision:** Ask Opus to add early return conditions. Opus fixes, re-delivers.

Deploy to Vercel. Done.

---

## Token Efficiency

**Linear approach (all Opus):**
- Planning: 200k tokens
- React component: 150k tokens
- Shader: 300k tokens
- Animation loop: 200k tokens
- Config: 50k tokens
- **Total: 900k tokens**

**Mahler approach (routed):**
- Phase 0 interrogation: 50k tokens
- Phase 1 (Fable): 80k tokens
- React + Canvas (Sonnet): 120k tokens
- Shader (Opus): 180k tokens
- Animation loop (Sonnet): 100k tokens
- Config (Haiku): 20k tokens
- **Total: 550k tokens** (39% savings)

---

## Key Takeaways

1. **Interrogation prevents false starts.** Knowing the shader exists saved decomposition time.
2. **Parallel execution is fast.** React + Haiku boilerplate ran while Opus tackled shaders.
3. **Routing saves tokens.** Haiku didn't need Opus intelligence for styling.
4. **Escalation works.** When the shader hit a performance issue, escalating to Opus was the right call.

---

## Try It Yourself

Start a Claude Code session and type `/mahler`. Give it your own creative project and let Mahler orchestrate.
