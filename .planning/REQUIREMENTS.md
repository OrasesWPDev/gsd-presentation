# Requirements: GSD Training Demo

**Defined:** 2026-01-26
**Core Value:** Inspire attendees to try GSD themselves by showing a complete, reproducible workflow that produces real working software in a short time.

## v1 Requirements

Requirements for initial demo delivery. Each maps to roadmap phases.

### Training Materials

- [x] **TRAIN-01**: Demo script with time budgets for 20-min presentation
- [x] **TRAIN-02**: Pre-written responses for GSD questioning phase (copy-paste ready)
- [x] **TRAIN-03**: Visual workflow diagram of GSD workflow (Mermaid flowchart)
- [ ] **TRAIN-04**: Copy-paste code snippets (tested and ready for live demo)

### Pomodoro Timer

- [x] **TIMER-01**: 25-minute countdown timer with timestamp-based logic
- [x] **TIMER-02**: Start/stop/reset button controls
- [x] **TIMER-03**: MM:SS display format
- [x] **TIMER-04**: Visual completion indicator (timer reaches 00:00)
- [x] **TIMER-05**: 5-minute break mode
- [x] **TIMER-06**: Session counter showing completed pomodoros

## v2 Requirements

Deferred to post-demo enhancement. Tracked but not in current roadmap.

### Pomodoro Enhancements

- **TIMER-07**: Audio completion beep using Web Audio API
- **TIMER-08**: Browser notifications on timer completion
- **TIMER-09**: Visual progress bar showing time remaining

### Training Enhancements

- **TRAIN-05**: Backup code at each phase checkpoint
- **TRAIN-06**: Troubleshooting reference card for common issues

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Task management | Scope creep — not a Pomodoro requirement |
| Analytics/statistics | Complexity kills demo momentum |
| User accounts | Way over scope for 20-min demo |
| Settings UI | Adds time without demo value |
| Multiple timer presets | Nice-to-have, not table stakes |
| Mobile-responsive design | Desktop demo only |
| Data persistence | Timer state doesn't need to survive refresh |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| TIMER-03 | Phase 1 | Complete |
| TIMER-01 | Phase 2 | Complete |
| TIMER-02 | Phase 2 | Complete |
| TIMER-04 | Phase 2 | Complete |
| TIMER-05 | Phase 3 | Complete |
| TIMER-06 | Phase 3 | Complete |
| TRAIN-01 | Phase 4 | Complete |
| TRAIN-02 | Phase 4 | Complete |
| TRAIN-03 | Phase 4 | Complete |
| TRAIN-04 | Phase 4 | Pending |

**Coverage:**
- v1 requirements: 10 total
- Mapped to phases: 10
- Unmapped: 0

---
*Requirements defined: 2026-01-26*
*Last updated: 2026-01-27 after phase 4 completion*
