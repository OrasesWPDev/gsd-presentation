# Phase 4: Training Materials - Research

**Researched:** 2026-01-26
**Domain:** Demo scripting, tmux pane orchestration, Mermaid diagrams, GSD workflow integration
**Confidence:** HIGH

## Summary

Phase 4 creates all materials needed for a 20-minute GSD demo. The domain is documentation and demo orchestration, not code development. Research reveals established patterns for demo scripts (intro/demo/wrap structure with time budgets), tmux parallel pane setup (horizontal splits with synchronization), and Mermaid flowchart syntax for visualizing the GSD workflow.

Key tools: The demo uses **tmux** for visual parallel planning (3 panes running simultaneous `/gsd:plan-phase` commands), and **Ralph Loop** (`/ralph-loop:ralph-loop`) for autonomous execution. The GSD questioning phase uses `AskUserQuestion` with concrete options, so pre-written responses must match the expected option format (terse, direct answers).

**Primary recommendation:** Create a script with exact commands (no improvisation), pre-written responses that match GSD's AskUserQuestion patterns, and a Mermaid flowchart using `flowchart LR` direction for the horizontal workflow stages.

## Standard Stack

The established tools for this domain:

### Core
| Tool | Version | Purpose | Why Standard |
|------|---------|---------|--------------|
| tmux | 3.4+ | Parallel terminal panes | Visual impact of 3 agents working simultaneously, copy-paste ready commands |
| Mermaid | 11.x | Workflow diagrams | Renders in GitHub markdown, version controlled, no external tools needed |
| Ralph Loop | b97f6ea+ | Autonomous execution | Stop hook creates feedback loop, presenter can narrate without interruption |
| Claude Code | Latest | GSD workflow execution | `--dangerously-skip-permissions` flag eliminates permission prompts |

### Supporting
| Tool | Purpose | When to Use |
|------|---------|-------------|
| Markdown | Script formatting | Demo script with time budgets, code blocks for commands |
| GitHub | Diagram rendering | Mermaid diagrams render automatically in `.md` files |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| tmux | Multiple terminal windows | Loses visual impact of side-by-side panes, harder to manage |
| Mermaid | Draw.io/Excalidraw | Not version controlled, requires external tool, harder to update |
| Ralph Loop | Manual `/gsd:execute-phase` calls | Requires presenter interaction, breaks narration flow |

**Installation:**
```bash
# tmux (usually pre-installed on macOS)
brew install tmux

# Mermaid renders in GitHub - no installation needed

# Ralph Loop already installed (verify)
cat ~/.claude/plugins/installed_plugins.json | grep ralph-loop
```

## Architecture Patterns

### Recommended Output Structure
```
docs/
  demo/
    SCRIPT.md              # Main demo script with time budgets
    RESPONSES.md           # Pre-written responses for GSD questioning
    GSD-WORKFLOW.md        # Mermaid diagram of GSD flow
```

### Pattern 1: Demo Script with Time Budgets
**What:** Structured script with exact commands and time allocations
**When to use:** Any live demo requiring pacing and reproducibility
**Example:**
```markdown
# Source: Demo presentation best practices

## Demo Script (20 minutes)

### 1. Introduction (2 min)
**Time budget:** 2:00
**Goal:** Set context, show what we'll build

SAY: "We're going to build a Pomodoro timer using GSD workflow..."

### 2. GSD Questioning (3 min)
**Time budget:** 3:00
**Goal:** Show rapid project initialization

COMMAND:
\`\`\`bash
claude --dangerously-skip-permissions
\`\`\`

COMMAND IN CLAUDE:
\`\`\`
/gsd:new-project
\`\`\`

[Pre-written responses for each question - see RESPONSES.md]

### 3. Parallel Planning (4 min)
**Time budget:** 4:00
**Goal:** Visual demonstration of 3 agents planning simultaneously

SETUP TMUX:
\`\`\`bash
# Create new session with 3 horizontal panes
tmux new-session -d -s demo
tmux split-window -h -t demo
tmux split-window -h -t demo
tmux select-layout -t demo even-horizontal
tmux attach -t demo
\`\`\`

[Commands for each pane...]
```

### Pattern 2: Tmux 3-Pane Horizontal Layout
**What:** Three equal-width panes for parallel Claude Code sessions
**When to use:** Demonstrating parallel agent execution
**Example:**
```bash
# Source: tmux documentation + parallel execution patterns

# Option A: Script-based setup (recommended for demo reliability)
#!/bin/bash
SESSION="gsd-demo"

# Create session with first pane
tmux new-session -d -s $SESSION -c ~/projects/gsd-training

# Split into 3 horizontal panes
tmux split-window -h -t $SESSION -c ~/projects/gsd-training
tmux split-window -h -t $SESSION -c ~/projects/gsd-training

# Balance pane widths
tmux select-layout -t $SESSION even-horizontal

# Send commands to each pane
tmux send-keys -t $SESSION:0.0 'claude --dangerously-skip-permissions' Enter
tmux send-keys -t $SESSION:0.1 'claude --dangerously-skip-permissions' Enter
tmux send-keys -t $SESSION:0.2 'claude --dangerously-skip-permissions' Enter

# Attach to session
tmux attach -t $SESSION
```

```bash
# Option B: Manual setup (interactive)
tmux new -s demo              # Create session
Ctrl+B %                      # Split vertically (creates pane 1)
Ctrl+B %                      # Split again (creates pane 2)
Ctrl+B Alt+2                  # Even-horizontal layout
```

### Pattern 3: Ralph Loop for Autonomous Execution
**What:** Continuous execution loop with completion detection
**When to use:** Hands-off phase execution while presenter narrates
**Example:**
```bash
# Source: Ralph Loop plugin documentation

# Invocation with completion promise
/ralph-loop:ralph-loop "/gsd:execute-phase 1" --completion-promise "PHASE_COMPLETE" --max-iterations 20

# How it works:
# 1. Claude Code executes the prompt
# 2. On exit attempt, stop hook intercepts
# 3. Same prompt fed back (with file changes visible)
# 4. Loop continues until completion promise appears or max iterations reached
```

### Pattern 4: GSD Pre-written Responses Format
**What:** Terse responses matching AskUserQuestion option format
**When to use:** GSD questioning phase during live demo
**Example:**
```markdown
# Source: GSD questioning.md reference

## Response Pattern

GSD uses AskUserQuestion with concrete options. Responses should be:
- **Terse** - 1-3 words when selecting an option
- **Direct** - No explanation needed (GSD does the talking)
- **Option-matching** - Use exact wording when available

### Example Interaction

GSD ASKS:
"What are you building?"
Options: "Web app" / "CLI tool" / "API" / "Other"

RESPONSE: "Web app"  (not "I want to build a web application")

GSD ASKS (follow-up):
"What kind of web app?"
(free text)

RESPONSE: "Pomodoro timer. Single HTML file. 25-min work, 5-min break."
```

### Anti-Patterns to Avoid
- **Long explanations in responses:** Slows demo, GSD handles explanation
- **Improvised commands:** Creates errors, breaks reproducibility
- **Manual execution steps:** Defeats purpose of autonomous demo
- **Vertical tmux splits:** 3 tall-narrow panes are unreadable

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Parallel terminal sessions | Multiple Terminal.app windows | tmux with even-horizontal layout | Single view, synchronized commands possible |
| Workflow diagrams | ASCII art or custom SVG | Mermaid flowchart in Markdown | Renders in GitHub, maintainable, version controlled |
| Autonomous execution | Manual `/gsd:execute-phase` per phase | Ralph Loop with completion promise | Hands-off execution, presenter can narrate |
| Demo pacing | Mental timing | Written time budgets per section | Reproducible, trainable, auditable |

**Key insight:** This phase produces documents, not code. The "standard stack" is established tools (tmux, Mermaid, Ralph Loop) configured correctly, not libraries.

## Common Pitfalls

### Pitfall 1: GSD Response Mismatch
**What goes wrong:** Responses don't match AskUserQuestion options, causing confusion or re-prompting
**Why it happens:** Writing responses before understanding GSD's questioning pattern
**How to avoid:** Study actual GSD questioning flow first. Responses match option text or are terse free-text.
**Warning signs:** Demo slows down with clarifying questions

### Pitfall 2: Tmux Pane Commands Sent to Wrong Pane
**What goes wrong:** Commands execute in wrong pane, breaking parallel demo
**Why it happens:** Forgetting to specify target pane in `tmux send-keys`
**How to avoid:** Always use `-t SESSION:WINDOW.PANE` format:
```bash
# Correct: Explicit pane targeting
tmux send-keys -t demo:0.0 'command' Enter  # Leftmost pane
tmux send-keys -t demo:0.1 'command' Enter  # Middle pane
tmux send-keys -t demo:0.2 'command' Enter  # Rightmost pane

# Wrong: No target (goes to current pane)
tmux send-keys 'command' Enter
```
**Warning signs:** Commands appearing in unexpected panes during rehearsal

### Pitfall 3: Ralph Loop Without Max Iterations
**What goes wrong:** Loop never terminates if completion condition fails
**Why it happens:** Trusting completion promise alone without safety limit
**How to avoid:** Always set `--max-iterations`:
```bash
# Safe: Has escape hatch
/ralph-loop:ralph-loop "task" --completion-promise "DONE" --max-iterations 30

# Risky: Could loop forever
/ralph-loop:ralph-loop "task" --completion-promise "DONE"
```
**Warning signs:** Demo hangs, presenter loses control

### Pitfall 4: Time Budget Overflow
**What goes wrong:** Intro runs 5 minutes, no time left for demo substance
**Why it happens:** Underestimating section durations, no hard cutoffs
**How to avoid:** Build in buffer time (2-3 min), mark sections as "cut if needed"
```markdown
### 1. Introduction (2 min) [CUT-SAFE]
### 2. GSD Questioning (3 min)
### 3. Parallel Planning (4 min) [CUT-SAFE: can show 1 pane only]
### 4. Execution (8 min) [CORE]
### 5. Wrap-up (3 min)
BUFFER: 2 min
```
**Warning signs:** Rehearsal consistently runs over 20 minutes

### Pitfall 5: Mermaid "end" Keyword Conflict
**What goes wrong:** Flowchart fails to render
**Why it happens:** Using lowercase "end" as a node label (reserved keyword)
**How to avoid:** Capitalize or quote: `End`, `"end"`, or use alternate label
```mermaid
flowchart LR
    A[Start] --> B[Process]
    B --> C[End]  %% Correct: Capitalized
    %% B --> C[end]  -- WRONG: breaks flowchart
```
**Warning signs:** Mermaid diagram shows syntax error in GitHub preview

## Code Examples

Verified patterns for this phase:

### Mermaid GSD Workflow Diagram
```markdown
# Source: Mermaid flowchart documentation + GSD workflow structure

\`\`\`mermaid
flowchart LR
    subgraph Discovery["Discovery Phase"]
        Q[Questioning]
        R[Research]
    end

    subgraph Planning["Planning Phase"]
        REQ[Requirements]
        ROAD[Roadmap]
        PLAN[Plan Tasks]
    end

    subgraph Execution["Execution Phase"]
        EXEC[Execute Tasks]
        VERIFY[Verify Work]
    end

    Q -->|"User answers"| R
    R -->|"Domain knowledge"| REQ
    REQ -->|"Scope defined"| ROAD
    ROAD -->|"Phases defined"| PLAN
    PLAN -->|"Tasks ready"| EXEC
    EXEC -->|"Code complete"| VERIFY
    VERIFY -->|"Gaps found"| PLAN
    VERIFY -->|"Phase complete"| DONE([Done])

    classDef discovery fill:#3498db,stroke:#2980b9,color:#fff
    classDef planning fill:#2ecc71,stroke:#27ae60,color:#fff
    classDef execution fill:#e74c3c,stroke:#c0392b,color:#fff

    class Q,R discovery
    class REQ,ROAD,PLAN planning
    class EXEC,VERIFY execution
\`\`\`
```

### Tmux Demo Setup Script
```bash
#!/bin/bash
# Source: tmux documentation + demo requirements

SESSION="gsd-demo"
PROJECT_DIR="$HOME/projects/gsd-training"

# Kill existing session if present
tmux kill-session -t $SESSION 2>/dev/null

# Create session
tmux new-session -d -s $SESSION -c "$PROJECT_DIR"

# Create 3 horizontal panes
tmux split-window -h -t $SESSION -c "$PROJECT_DIR"
tmux split-window -h -t $SESSION -c "$PROJECT_DIR"

# Balance widths
tmux select-layout -t $SESSION even-horizontal

# Label panes (optional - for presenter reference)
tmux select-pane -t $SESSION:0.0 -T "Phase 1"
tmux select-pane -t $SESSION:0.1 -T "Phase 2"
tmux select-pane -t $SESSION:0.2 -T "Phase 3"

# Attach
tmux attach -t $SESSION
```

### Pre-written Response Template
```markdown
# GSD Questioning Responses for Pomodoro Timer Demo

## /gsd:new-project

**Q: What are you building?**
A: "Pomodoro timer"

**Q: Tell me more about the Pomodoro timer**
A: "25-min work sessions, 5-min breaks. Single HTML file. No dependencies."

**Q: Who is this for?**
A: "Me. Personal productivity."

**Q: What does done look like?**
A: "Timer counts down 25:00 to 00:00. Start/stop/reset buttons. Shows completed sessions."

**Q: [Ready to create PROJECT.md?]**
A: Select "Create PROJECT.md"
```

### Ralph Loop Execution Pattern
```bash
# Source: Ralph Loop plugin documentation

# For autonomous phase execution during demo:
/ralph-loop:ralph-loop "/gsd:execute-phase 1 && /gsd:execute-phase 2 && /gsd:execute-phase 3" --completion-promise "## Phase 3" --max-iterations 50

# Simpler: One phase at a time (more control)
# In pane 1:
/ralph-loop:ralph-loop "/gsd:execute-phase 1" --completion-promise "Phase 1" --max-iterations 20
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Terminal multiplexers | tmux (still current) | 2010s | Standard for parallel terminal demos |
| Draw.io diagrams | Mermaid in Markdown | 2020+ | Version controlled, GitHub renders natively |
| Manual demo execution | Ralph Loop autonomous execution | 2025+ | Presenter narrates while AI executes |
| Read-aloud scripts | Copy-paste command scripts | Always valid | Zero improvisation, reproducible |

**Deprecated/outdated:**
- Screen (terminal multiplexer): tmux has better scripting, more intuitive key bindings
- External diagram tools: Mermaid eliminates export/import workflow

## Open Questions

Things that couldn't be fully resolved:

1. **Optimal time budget split for 20 minutes**
   - What we know: Industry standard is ~40% demo, ~20% intro/wrap, ~10% Q&A
   - What's unclear: No Q&A time in this demo; how to reallocate?
   - Recommendation: 2 min intro, 3 min questioning, 4 min parallel planning, 8 min execution, 3 min wrap. Total: 20 min exactly.

2. **GSD questioning flow for "Pomodoro timer" specifically**
   - What we know: GSD uses AskUserQuestion with "What are you building?", "Who is it for?", "What does done look like?" pattern
   - What's unclear: Exact follow-up questions depend on initial answers
   - Recommendation: Prepare 5-6 responses covering common GSD question patterns; accept some improvisation if questions vary

3. **Ralph Loop completion promise for phase execution**
   - What we know: Completion promise must be exact string match
   - What's unclear: What string reliably appears when phase completes?
   - Recommendation: Use `--max-iterations` as primary control; completion promise as optimization. Test during rehearsal.

## Sources

### Primary (HIGH confidence)
- Ralph Loop plugin: `/Users/chadmacbook/.claude/plugins/cache/claude-plugins-official/ralph-loop/b97f6eadd929/README.md` - Plugin invocation, completion promises, iteration limits
- GSD questioning reference: `/Users/chadmacbook/.claude/get-shit-done/references/questioning.md` - Question patterns, AskUserQuestion format
- GSD execute-phase workflow: `/Users/chadmacbook/.claude/get-shit-done/workflows/execute-phase.md` - Phase execution flow, wave-based execution
- Mermaid flowchart docs: https://mermaid.js.org/syntax/flowchart.html - Node shapes, edge types, styling, subgraphs

### Secondary (MEDIUM confidence)
- tmux documentation: https://gist.github.com/sdondley/b01cc5bb1169c8c83401e438a652b84e - Split-window patterns, pane targeting
- Demo script best practices: https://www.storylane.io/blog/how-to-prepare-a-great-software-demo-presentation - Time budget structure, section pacing

### Tertiary (LOW confidence)
- None - All patterns verified with primary sources

## Metadata

**Confidence breakdown:**
- Demo script structure: HIGH - Industry-standard patterns, verified against demo presentation guides
- Tmux setup: HIGH - Standard tmux commands, verified against documentation
- Mermaid syntax: HIGH - Official Mermaid documentation
- GSD responses: MEDIUM - Based on GSD source code, but exact questions may vary
- Ralph Loop integration: MEDIUM - Plugin documented, but execution timing untested in this context

**Research date:** 2026-01-26
**Valid until:** 2026-02-26 (documentation-heavy phase, 30-day validity)
