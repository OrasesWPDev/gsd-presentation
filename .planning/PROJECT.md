# GSD Training Demo

## What This Is

A training package for demonstrating GSD (Get Shit Done) workflow to a mixed audience of developers, business analysts, and project managers during a 20-minute weekly company call. The deliverables include a demo script, pre-written responses for efficient live demos, a visual workflow diagram, and a working minimal Pomodoro timer application.

## Core Value

Inspire attendees to try GSD themselves by showing a complete, reproducible workflow that produces real working software in a short time.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Training script that fits 20-minute timebox
- [ ] Pre-written responses for GSD questioning phase (copy-paste ready)
- [ ] Visual workflow diagram of the Pomodoro timer (using BA visual-workflow-diagram-generator skill)
- [ ] Working minimal Pomodoro timer (HTML/JS, runs locally in browser)
- [ ] Demo shows full GSD flow: questioning → research → requirements → roadmap → plan → execute
- [ ] Materials work for mixed audience (devs see code, BAs/PMs see planning artifacts)
- [ ] Reproducible in fresh directory (user can replay demo from scratch)

### Out of Scope

- Advanced Pomodoro features (stats, breaks, customizable durations) — keep minimal for demo clarity
- Pre-recorded video — demo will be live with copy-paste for speed
- Gmail organizer or other API-dependent projects — OAuth complexity risks derailing live demo
- Mobile app or complex deployment — local browser execution only

## Context

- Company runs weekly Claude Code training calls
- User enjoys GSD and wants to showcase it to colleagues
- Audience is mixed: developers (technical), business analysts, project managers (non-technical)
- The visual workflow diagram skill will hook non-developers by showing planning artifacts visually
- Live demo format with pre-written responses to save time while maintaining authenticity
- User wants to experience the GSD process themselves first, then have materials ready for replay

**Visual Workflow Skill Reference:**
https://github.com/OrasesLabs/orases-claude-code-marketplace/blob/main/plugins/ba-toolkit/skills/visual-workflow-diagram-generator/SKILL.md

## Constraints

- **Time**: 20 minutes total demo time — every phase must be compressed
- **Audience**: Must resonate with both technical and non-technical attendees
- **Environment**: Google Workspace company, local execution only, no external API dependencies
- **Reproducibility**: Must work in a fresh directory for actual demo

## Demo & Development Notes

**For both demo and development:** Launch Claude Code with the `--dangerously-skip-permissions` flag to avoid permission prompts that would slow down the demo or interrupt flow.

```bash
claude --dangerously-skip-permissions
```

This flag bypasses tool permission confirmations, enabling rapid execution during live demos and faster iteration during development.

### Parallelization Guide

**What CAN be parallelized:**
- ✓ **Planning phases 1-3** — run `/gsd:plan-phase 1 2 3` to plan all timer phases at once
- ✓ **Research** — researchers spawn in parallel during planning

**What CANNOT be parallelized:**
- ✗ **Execution of phases 1-3** — must run sequentially (each builds on previous)
- ✗ **Phase 4** — needs completed timer code from phases 1-3

**Why sequential execution?** All timer phases modify the same file (`pomodoro-timer/index.html`):
- Phase 1 creates HTML + state object
- Phase 2 adds timer functions (needs Phase 1's state)
- Phase 3 adds mode switching (needs Phase 2's functions)

**Demo execution order:**
```
/gsd:execute-phase 1  → wait for completion
/gsd:execute-phase 2  → wait for completion
/gsd:execute-phase 3  → wait for completion
/gsd:execute-phase 4  → training materials
```

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Pomodoro timer over Gmail organizer | OAuth/API complexity risks derailing live demo | — Pending |
| Minimal features only | Demo clarity over feature richness | — Pending |
| Full GSD flow in demo | Show complete workflow to inspire adoption | — Pending |
| Live with copy-paste | Authenticity of live demo + time efficiency | — Pending |
| Use visual workflow skill | Hooks non-technical audience (BAs/PMs) | — Pending |

---
*Last updated: 2026-01-26 after initialization*
