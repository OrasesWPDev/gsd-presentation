# GSD Demo Script: Pomodoro Timer

**Total runtime:** 20 minutes
**Audience:** Mixed technical/non-technical
**Demo output:** Working Pomodoro timer built live using GSD workflow

---

## Pre-Demo Setup

Before the demo, ensure the following are ready:

```bash
# 1. Clean project directory (or create fresh one)
cd ~/projects/gsd-training/present-live
rm -rf .planning index.html 2>/dev/null

# 2. Verify Claude Code is installed
claude --version
```

---

## Section 1: Introduction

**Time budget:** 2:00

### SAY:

"Today I'm going to build a Pomodoro timer from scratch using the GSD workflow.

**What is GSD?** It's a lightweight and powerful meta-prompting, context engineering, and spec-driven development system for Claude Code. It helps solve context rot — the quality degradation that happens as Claude fills its context window.

GSD keeps Claude focused by breaking work into documented phases with clear handoffs.

By the end of this 20-minute demo, we'll have a working timer with:
- 30-second work sessions (short so you can see it complete)
- Start, Stop, and Reset buttons
- Session counter that increments when work completes

I won't be writing code. Claude does that. My job is to answer questions and watch it work."

### DO:

- Show the GSD workflow diagram (see [GSD-WORKFLOW.md](./GSD-WORKFLOW.md))
- Point out the stages: Questioning, Planning, Execution (we'll skip Research & Verification for speed)

---

## Section 2: GSD Questioning [CUT-SAFE]

**Time budget:** 3:00

### SAY:

"First, we initialize the project. GSD will ask me a few questions to understand what I want to build."

### COMMAND:

```bash
cd ~/projects/gsd-training
claude --dangerously-skip-permissions
```

### COMMAND (in Claude):

```
/gsd:new-project
```

### DO:

Answer GSD questions. Questions will vary — these are based on actual test runs.

**Response strategy:** Keep answers short (under 20 words). Stick to these facts:
- Single HTML file, no frameworks, runs in browser
- 30-second work, 10-second breaks (short times for demo visibility)
- Start/Stop/Reset buttons, session counter
- Demo tool to show GSD workflow

---

**Typical question flow:**

**Q: What are you building?**
```
Pomodoro timer
```

**Q: How will you use this? / Walk me through a work session...**
```
Demo for colleagues. Short 30-second timers so audience can see it complete.
```

**Q: When a pomodoro ends, what should happen? Sound? Visual? Notification? Track sessions?**
```
Visual indicator only, no sound. Yes, track completed sessions with a counter.
```

**Q: Classic pomodoro or simple countdown? What does visual indicator look like?**
```
30-second work, 10-second break. Short for demo. Timer turns green when done.
```

**Q: What controls/buttons?**
```
Start, Stop, and Reset buttons. Plus mode buttons for Work and Break.
```

**[MULTIPLE CHOICE] Timer Flow — what happens when work timer ends?**
→ Select **"Manual control"** — user clicks to start break

**[MULTIPLE CHOICE] Pause Control**
→ Select option that **allows pausing** (Stop button)

**Q: What is a "session" for counting — work period or full work+break cycle?**
```
Work period only. Break completion doesn't count.
```

**Q: UI styling preferences?**
```
Minimal. Dark background, light text. Monospace timer display.
```

**Q: Ready to create PROJECT.md?**
→ Select **"Create PROJECT.md"**

---

**GSD Configuration (after PROJECT.md created):**

**[MULTIPLE CHOICE] Mode — How do you want to work?**
→ Select **"YOLO (Recommended)"** — auto-approve, just execute

**[MULTIPLE CHOICE] Depth**
→ Select **"Quick"**

**[MULTIPLE CHOICE] Execution**
→ Select **"Parallel"**

**[MULTIPLE CHOICE] Git Tracking**
→ Select **"Yes"**

**[MULTIPLE CHOICE] Research — Spawn Plan Researcher before planning each phase?**
→ Select **"No"** — simple project, skip for speed

**[MULTIPLE CHOICE] Plan Check**
→ Select **"No"** — skip for speed

**[MULTIPLE CHOICE] Verifier**
→ Select **"No"** — skip for speed

---

**After config, GSD asks about domain research:**

**[MULTIPLE CHOICE] Research — Research the domain ecosystem before defining requirements?**
→ Select **"Skip research"** — saves time, Pomodoro timer is a simple domain

---

**Additional questions (if asked):**

**Q: Who is this for?**
```
Me. Personal productivity.
```

**Q: Any constraints?**
```
Single HTML file. No frameworks. Runs locally in browser.
```

**Q: What tech stack?**
```
Vanilla JS, HTML, CSS. No build tools.
```

**Q: Any other features?**
```
No. Keep it minimal.
```

---

**For any unexpected question:** Answer in under 20 words, stay within the project scope above.

### SAY (after PROJECT.md created):

"GSD has captured my requirements. Notice I didn't write any requirements doc - it generated PROJECT.md from our conversation."

---

## Section 3: Parallel Planning

**Time budget:** 4:00

### SAY:

"Now watch this. GSD breaks the project into phases. Instead of planning them one at a time, I'll tell it to plan all three in parallel."

### COMMAND (in same Claude session):

```
/gsd:plan-phase 1 2 3 in parallel
```

GSD will spawn 3 planner agents simultaneously - one for each phase.

### SAY (while planning runs):

"GSD spawned 3 planner agents - one for each phase. They're all working simultaneously. Phase 1 is the timer foundation. Phase 2 is the countdown logic. Phase 3 adds session tracking.

We skipped research and verification in config to keep it fast."

### DO:

- Watch the 3 planners complete
- Point out when each PLAN.md is created

### SAY (after all complete):

"Planning is done. We now have detailed task lists for each phase. Let's execute them."

---

## Section 4: Execution [CORE]

**Time budget:** 8:00

### SAY:

"This is where it gets interesting. I'm going to use Ralph Loop to run all phases autonomously. Ralph Loop keeps Claude executing until the work is done - I don't have to keep prompting it."

### COMMAND (start Ralph Loop):

After planning completes, `/clear` then run:

```
/ralph-loop:ralph-loop "Execute GSD workflow for phases 1, 2, 3 sequentially. For each phase: /gsd:execute-phase. Complete each phase fully before moving to next. Output PHASES_COMPLETE when all 3 phases done." --max-iterations 50 --completion-promise "PHASES_COMPLETE"
```

Ralph Loop will iterate through all 3 phases autonomously - executing each phase in order until completion.

### SAY (while phases execute):

"I started Ralph Loop once with all three phases. Watch what happens:

**Phase 1** - Claude builds the HTML structure, CSS styling, and JavaScript state. Each task gets verified before moving on.

**Phase 2** - The countdown logic. GSD chose timestamp-based counting instead of simple interval decrement because intervals drift over time.

**Phase 3** - Mode switching and session counter. The timer will switch between work and break modes automatically."

### DO:

- Watch all 3 phases execute autonomously
- Point out transitions between phases as they happen
- Show the terminal output when all phases complete

---

## Section 5: Wrap-up

**Time budget:** 3:00

### SAY:

"Let's see what we built."

### COMMAND:

```bash
open ./index.html
```

### DO:

- Click Start - timer counts down from 0:30
- Click Stop - timer pauses
- Click Reset - timer resets to 0:30
- **Let it run to 0:00** - show session counter increment (only 30 seconds!)
- Click Break mode, start it, show mode switching

### SAY:

In 20 minutes, we went from zero to a working Pomodoro timer. I answered a few questions. Claude wrote all the code and committed everything.

You just saw the session counter increment — that's why we used 30-second timers. In a real Pomodoro app, you'd use 25 minutes.

This is the GSD workflow:
1. **Questioning** - Capture requirements through conversation
2. **Planning** - Break work into phases and tasks (we ran 3 planners in parallel)
3. **Execution** - Claude executes each task and commits it

For this demo we skipped research and verification to save time. In a real project, those add quality gates.

The key insight: I didn't write code. I answered questions and watched Claude work. GSD solved context rot by documenting everything in .planning/ files — Claude could `/clear` and resume without losing track.

### DO:

- Show the .planning directory structure
- Show a SUMMARY.md file from one of the phases
- Point out the git log with atomic commits

### SAY:

"Every task has its own commit. You can git bisect to find exactly where something broke. The SUMMARY files document what was built and any decisions made.

Questions?"

---

## Contingency Notes

### If time runs short:

1. **Section 2 (Questioning)** is [CUT-SAFE] - can skip if PROJECT.md pre-exists
2. **Section 4 (Execution)** - can pre-execute Phase 1, show only Phase 2 and 3 live

### If something fails:

- Ralph Loop has max-iterations as safety net
- Individual task failures get auto-fixed by GSD
- If stuck, show the partial progress and explain the retry mechanism

### Common issues:

1. **Claude permissions:** Use `--dangerously-skip-permissions` flag
2. **Phase fails:** Check .planning/phases/XX/PLAN.md for task status
3. **Ralph Loop stuck:** Press Escape, then retry with `/gsd:execute-phase 1` manually

---

## Files Reference

- [GSD-WORKFLOW.md](./GSD-WORKFLOW.md) - Mermaid diagram of GSD stages (for Section 1 visual)
