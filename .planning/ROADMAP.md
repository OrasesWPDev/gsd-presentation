# Roadmap: GSD Training Demo

## Overview

This roadmap delivers a complete GSD workflow demonstration package in 4 phases. Phases 1-3 build the Pomodoro timer incrementally: foundation (static UI + state + display), core logic (countdown timer with controls), and polish (mode switching + session tracking). Phase 4 creates training materials (demo script, pre-written responses, visual workflow diagram, code snippets) based on the working timer code. Each phase delivers visible, demonstrable value that supports the 20-minute live demo goal.

## Parallelization

**Planning:** Phases 1-3 (all timer phases) can be planned in parallel — they share the same artifact design.

**Execution:** Timer phases execute sequentially (each builds on previous), then Phase 4 extracts training materials from the completed timer.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3, 4): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Timer Foundation** - Static HTML/CSS structure, state management, and display formatting
- [x] **Phase 2: Core Timer Logic** - Countdown mechanics with start/stop controls and completion detection
- [x] **Phase 3: Modes and Polish** - Break mode switching and session counter
- [x] **Phase 4: Training Materials** - Demo script, pre-written responses, and GSD workflow diagram

## Phase Details

### Phase 1: Timer Foundation
**Goal**: Static timer UI exists with functional display formatting
**Depends on**: Nothing (first phase)
**Requirements**: TIMER-03
**Success Criteria** (what must be TRUE):
  1. User sees centered timer display showing "25:00" in large, readable format
  2. User sees mode buttons (Pomodoro, Short Break) in the interface
  3. User sees start/stop button in the interface
  4. Code has state object holding timer configuration (durations, current mode)
  5. Display updates when state changes (updateDisplay function works)
**Plans**: 1 plan

Plans:
- [x] 01-01-PLAN.md — Create timer HTML/CSS structure with state management and updateDisplay

### Phase 2: Core Timer Logic
**Goal**: Timer counts down from 25:00 to 00:00 with user controls
**Depends on**: Phase 1
**Requirements**: TIMER-01, TIMER-02, TIMER-04
**Success Criteria** (what must be TRUE):
  1. User clicks Start and timer counts down from 25:00 in real-time
  2. User clicks Stop and countdown pauses at current time
  3. User clicks Reset and timer returns to 25:00
  4. Timer uses timestamp-based calculation (no drift over 25 minutes)
  5. Timer reaches 00:00 and shows completion state clearly
**Plans**: 1 plan

Plans:
- [x] 02-01-PLAN.md — Implement countdown logic and controls with completion detection

### Phase 3: Modes and Polish
**Goal**: Users can switch between work and break modes with session tracking
**Depends on**: Phase 2
**Requirements**: TIMER-05, TIMER-06
**Success Criteria** (what must be TRUE):
  1. User clicks "Short Break" button and timer switches to 5:00 countdown
  2. User clicks "Pomodoro" button and timer switches back to 25:00 countdown
  3. Session counter increments when Pomodoro completes (reaches 00:00)
  4. Session counter displays "Pomodoros completed: X" prominently
  5. Mode switching preserves timer state (if running, keeps running with new duration)
**Plans**: 1 plan

Plans:
- [x] 03-01-PLAN.md — Add mode switching and session counter

### Phase 4: Training Materials
**Goal**: Presenters have everything needed to deliver the 20-minute demo
**Depends on**: Phase 3 (needs completed timer code)
**Requirements**: TRAIN-01, TRAIN-02, TRAIN-03
**Success Criteria** (what must be TRUE):
  1. Demo script exists with time budgets totaling 20 minutes (intro, questioning, planning, execution, wrap)
  2. Pre-written GSD responses are copy-paste ready for questioning phase
  3. Visual workflow diagram exists showing GSD stages (Questioning, Planning, Execution, Verification)
**Plans**: 1 plan

Plans:
- [x] 04-01-PLAN.md — Create demo script, pre-written responses, and GSD workflow diagram

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Timer Foundation | 1/1 | ✓ Complete | 2026-01-26 |
| 2. Core Timer Logic | 1/1 | ✓ Complete | 2026-01-26 |
| 3. Modes and Polish | 1/1 | ✓ Complete | 2026-01-26 |
| 4. Training Materials | 1/1 | ✓ Complete | 2026-01-27 |
