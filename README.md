# GSD Presentation Demo

A 20-minute live demo showing the [GSD (Get Shit Done)](https://github.com/punkpeye/gsd) workflow by building a Pomodoro timer from scratch.

## Quick Reference

| File | Purpose |
|------|---------|
| [docs/demo/SCRIPT.md](docs/demo/SCRIPT.md) | Presentation script with time budgets and prompts |
| [docs/demo/RESPONSES.md](docs/demo/RESPONSES.md) | Pre-written answers for GSD questioning phase |
| [docs/demo/GSD-WORKFLOW.md](docs/demo/GSD-WORKFLOW.md) | Visual workflow diagram (Mermaid) |

## Running the Demo

```bash
cd ~/projects/gsd-training/present-live
rm -rf .planning index.html 2>/dev/null
claude --dangerously-skip-permissions
```

Then follow [SCRIPT.md](docs/demo/SCRIPT.md).

## What is GSD?

A lightweight meta-prompting, context engineering, and spec-driven development system for Claude Code. Solves context rot — the quality degradation that happens as Claude fills its context window.
