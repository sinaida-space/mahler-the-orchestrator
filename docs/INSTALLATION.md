# Installation Guide

## Prerequisites

- Claude Code (CLI, desktop app, or IDE extension)
- A Claude Code session ready to go
- Git (optional, but easier)

## Option 1: Quick Install (Recommended)

```bash
# Clone the repository
git clone https://github.com/sinaida-space/mahler-the-orchestrator.git

# Copy the plugin to your Claude Code skills directory
cp -r mahler-the-orchestrator ~/.claude/skills/mahler

# If the directory doesn't exist yet, create it:
# mkdir -p ~/.claude/skills
```

## Option 2: Manual Install

1. Download the repository as ZIP
2. Extract it
3. Copy the `mahler` folder to `~/.claude/skills/`

Your directory structure should look like:
```
~/.claude/skills/
├── mahler/
│   ├── .claude-plugin/
│   │   └── plugin.json
│   ├── agents/
│   ├── commands/
│   └── skills/
```

## Verify Installation

Start a **new Claude Code session** and run:

```bash
claude plugin list
```

You should see:
```
Skills-directory plugins (.claude/skills/*):

  ❯ mahler@skills-dir
    Version: 1.0.0
    Scope: user
    Status: ✔ loaded
```

## First Time Using Mahler

Type `/mahler` in a Claude Code session to launch the orchestrator.

If you get "unknown command," you may need to:
1. Restart your Claude Code session
2. Check that the plugin is in the correct directory (`~/.claude/skills/mahler`)
3. Run `claude plugin list` to verify it loaded

## Troubleshooting

### "Plugin not found"
- Make sure the folder is named exactly `mahler` (lowercase)
- Verify it's in `~/.claude/skills/`, not somewhere else
- Restart Claude Code

### "/mahler command doesn't work"
- The command loads on session startup. Restart your session.
- Check `claude plugin list` — the plugin should show "✔ loaded"

### "Model not available"
- Pro plan? Mahler degrades gracefully using your current model
- Check your plan — Opus/Fable require higher tiers or API access

---

## What Gets Installed

The plugin includes:

| Component | Purpose |
|-----------|---------|
| `/mahler` command | Launch the 4-phase orchestrator |
| Creative Director agent | Brainstorm + decompose + route tasks |
| Developer agents | Execute on Opus/Sonnet/Haiku |
| Routing skill | Decision tree for model assignment |
| Reference docs | Interrogation protocol, routing guide |

## Next Steps

1. Read the [README](../README.md) for context
2. Try `/mahler` on a real project
3. Check [ROUTING.md](ROUTING.md) if you want to understand model assignments
4. Refer to [INTERROGATION.md](INTERROGATION.md) for Phase 0 best practices
