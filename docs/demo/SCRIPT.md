# GSD Demo Script: Pomodoro Timer

**Total runtime:** 20 minutes
**Audience:** Mixed technical/non-technical
**Demo output:** Working Pomodoro timer built live using GSD workflow

---

## Pre-Demo Setup

Before the demo, ensure the following are ready:

```bash
# 1. Clean project directory
cd ~/projects/gsd-training
rm -rf .planning src index.html 2>/dev/null

# 2. Verify Claude Code is installed
claude --version

# 3. Verify tmux is available
tmux -V
```

---

## Section 1: Introduction

**Time budget:** 2:00

### SAY:

"Today I'm going to build a Pomodoro timer from scratch using the GSD workflow. GSD stands for Get Shit Done - it's a structured approach that uses Claude to handle planning and execution automatically.

By the end of this 20-minute demo, we'll have a working timer with:
- 25-minute work sessions
- 5-minute breaks
- Start, stop, and reset controls
- Session counter

I won't be writing code. Claude does that. My job is to answer questions and watch it work."

### DO:

- Show the GSD workflow diagram (see [GSD-WORKFLOW.md](./GSD-WORKFLOW.md))
- Point out the stages: Questioning, Research, Planning, Execution, Verification

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
- 25-min work, 5-min breaks
- Start/stop/reset controls, session counter
- Personal productivity tool

---

**Typical question flow:**

**Q: What are you building?**
```
Pomodoro timer
```

**Q: How will you use this? / Walk me through a work session...**
```
Browser tab. Open the HTML file and keep it visible while working.
```

**Q: When a pomodoro ends, what should happen? Sound? Visual? Notification? Track sessions?**
```
Visual indicator only, no sound. Yes, track completed sessions with a counter.
```

**Q: Classic pomodoro or simple countdown? What does visual indicator look like?**
```
25 min work, 5 min break. Simple, no long break cycle. Visual indicator: timer turns green when done.
```

**[MULTIPLE CHOICE] Timer Flow — what happens when work timer ends?**
→ Select **"Auto-start break"**

**[MULTIPLE CHOICE] Pause Control**
→ Select option that **allows pausing**

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

"Now watch this. GSD breaks the project into phases. Instead of planning them one at a time, we'll run three Claude instances in parallel. Each one will plan a different phase simultaneously."

### COMMAND (tmux setup):

```bash
SESSION="gsd-demo"
PROJECT_DIR="$HOME/projects/gsd-training"
tmux kill-session -t $SESSION 2>/dev/null
tmux new-session -d -s $SESSION -c "$PROJECT_DIR"
tmux split-window -h -t $SESSION -c "$PROJECT_DIR"
tmux split-window -h -t $SESSION -c "$PROJECT_DIR"
tmux select-layout -t $SESSION even-horizontal
tmux attach -t $SESSION
```

### COMMAND (start Claude in each pane):

**Pane 0 (left):**
```bash
tmux send-keys -t gsd-demo:0.0 'claude --dangerously-skip-permissions' Enter
```

**Pane 1 (middle):**
```bash
tmux send-keys -t gsd-demo:0.1 'claude --dangerously-skip-permissions' Enter
```

**Pane 2 (right):**
```bash
tmux send-keys -t gsd-demo:0.2 'claude --dangerously-skip-permissions' Enter
```

### COMMAND (send planning commands):

Wait for all 3 Claude instances to load, then:

**Pane 0:**
```bash
tmux send-keys -t gsd-demo:0.0 '/gsd:plan-phase 1' Enter
```

**Pane 1:**
```bash
tmux send-keys -t gsd-demo:0.1 '/gsd:plan-phase 2' Enter
```

**Pane 2:**
```bash
tmux send-keys -t gsd-demo:0.2 '/gsd:plan-phase 3' Enter
```

### SAY (while planning runs):

"Each pane is planning a different phase. Phase 1 is the timer foundation - HTML, CSS, state structure. Phase 2 is the core timer logic - countdown, start/stop. Phase 3 adds modes - work, short break, long break.

Sequential planning would take about 3 minutes. Parallel planning finishes in about 1 minute."

### DO:

- Watch all 3 panes complete
- Point out the PLAN.md files created in each phase directory

### SAY (after all complete):

"Planning is done. We now have detailed task lists for each phase. Let's execute them."

---

## Section 4: Execution [CORE]

**Time budget:** 8:00

### SAY:

"This is where it gets interesting. I'm going to use Ralph Loop to run all phases autonomously. Ralph Loop keeps Claude executing until the work is done - I don't have to keep prompting it."

### COMMAND (close extra panes):

Press `Ctrl+B` then type `:kill-pane` in panes 1 and 2, or:

```bash
tmux kill-pane -t gsd-demo:0.2
tmux kill-pane -t gsd-demo:0.1
```

Now you have one pane. If Claude exited, restart:

```bash
claude --dangerously-skip-permissions
```

### COMMAND (start Ralph Loop for all 3 phases):

```
/ralph-loop:ralph-loop "Execute GSD phases 1, 2, 3 in sequence:

Phase 1: /gsd:execute-phase 1 - Timer Foundation
Phase 2: /gsd:execute-phase 2 - Core Timer Logic
Phase 3: /gsd:execute-phase 3 - Modes and Polish

Run each phase to completion before starting the next.
Only output PHASE_COMPLETE after phase 3 verification passes." --completion-promise "PHASE_COMPLETE" --max-iterations 50
```

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
open ./pomodoro-timer/index.html
```

### DO:

- Click Start - timer counts down
- Click Stop - timer pauses
- Click Reset - timer resets to 25:00
- Let it run to completion (or set shorter times for demo)
- Show mode switching

### SAY:

"In 20 minutes, we went from zero to a working Pomodoro timer. I answered 5 questions. Claude wrote all the code, ran all the tests, and committed everything.

This is the GSD workflow:
1. **Questioning** - Capture requirements through conversation
2. **Planning** - Break work into phases and tasks (we ran 3 planners in parallel)
3. **Execution** - Claude executes each task, verifies it, commits it
4. **Verification** - Each phase has success criteria that must pass

The key insight: I didn't write code. I answered questions and watched Claude work. This is what AI-assisted development looks like in 2025."

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
2. **Section 3 (Parallel Planning)** can show just 1 pane instead of 3
3. **Section 4 (Execution)** - can pre-execute Phase 1, show only Phase 2 and 3 live

### If something fails:

- Ralph Loop has max-iterations as safety net
- Individual task failures get auto-fixed by GSD
- If stuck, show the partial progress and explain the retry mechanism

### Common issues:

1. **tmux not found:** `brew install tmux`
2. **Claude permissions:** Use `--dangerously-skip-permissions` flag
3. **Phase fails:** Check .planning/phases/XX/PLAN.md for task status

---

## Files Reference

- [GSD-WORKFLOW.md](./GSD-WORKFLOW.md) - Mermaid diagram of GSD stages (for Section 1 visual)
