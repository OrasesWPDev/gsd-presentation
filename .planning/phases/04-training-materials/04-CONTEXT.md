# Phase 4: Training Materials - Context

**Gathered:** 2026-01-26
**Status:** Ready for planning

<domain>
## Phase Boundary

Create everything presenters need to deliver a 20-minute GSD demo. Includes demo script, pre-written responses, and workflow diagram. The demo uses parallel planning (tmux) and autonomous execution (Ralph Loop) to maximize efficiency.

</domain>

<decisions>
## Implementation Decisions

### Demo Script Structure
- Full script with exact commands (copy-paste ready, nothing left to memory)
- Include exact tmux commands, keystrokes, and slash commands
- Time budgets per section (intro, parallel planning, execution, wrap-up)

### Parallel Planning Setup
- Use tmux with 3 panes (no git worktrees needed)
- Each pane runs separate Claude Code session with `--dangerously-skip-permissions`
- Pane 1: `/gsd:plan-phase 1`
- Pane 2: `/gsd:plan-phase 2`
- Pane 3: `/gsd:plan-phase 3`
- All 3 planners run simultaneously, each writes to own directory

### Execution Approach
- Use Ralph Loop plugin (`/ralph-loop:ralph-loop`) for autonomous execution
- Phases execute sequentially (1 → 2 → 3) since they're dependent
- Presenter narrates while Claude executes hands-off
- No manual checkpoint approvals needed during demo

### Pre-written Responses
- Minimal/terse tone — short answers, let GSD do the talking
- Just enough to answer questioning phase, not educational explanations
- Faster demo pace is priority

### Workflow Diagram
- Visualize GSD workflow stages (Questioning → Planning → Execution → Verification)
- NOT the Pomodoro timer architecture (that's not the demo's point)
- Use Mermaid in markdown (version controlled, renders in GitHub)

### Code Snippets
- Skip entirely — execution is live, snippets would be redundant

### Claude's Discretion
- Exact time budget allocation within 20-minute constraint
- Mermaid diagram styling and layout
- Script formatting and section headers

</decisions>

<specifics>
## Specific Ideas

- "Parallel planning cuts time from ~3 minutes sequential to ~1 minute parallel"
- tmux provides visual impact — audience sees 3 agents working simultaneously
- Ralph Loop makes execution feel magical — presenter talks, code appears

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 04-training-materials*
*Context gathered: 2026-01-26*
