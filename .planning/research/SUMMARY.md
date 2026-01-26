# Project Research Summary

**Project:** GSD Training Demo - Minimal Pomodoro Timer
**Domain:** Live coding demonstration (20-minute demo for mixed audience)
**Researched:** 2026-01-26
**Confidence:** HIGH

## Executive Summary

This project is a live coding demonstration designed to inspire adoption of the GSD workflow by building a minimal Pomodoro timer in 20 minutes before a mixed audience of developers, BAs, and PMs. Research reveals that the most critical success factor is ruthless simplicity: vanilla HTML/CSS/JavaScript in a single file with zero build steps enables instant feedback, universal comprehension, and eliminates setup overhead that would kill demo momentum.

The recommended approach is a single-file architecture (index.html with embedded CSS and JS) implementing only table-stakes features: 25-minute countdown, start/stop button, visual display, and completion alert. This delivers a working Pomodoro timer in approximately 15-18 minutes of coding, leaving time for introduction and wrap-up. The key insight from research is that every feature beyond core timing creates three risks: planning time (reduces wow-factor), implementation time (risks incomplete demo), and explanation burden (confuses non-technical audience).

The primary risk is demo time explosion from scope creep, technical failures, or audience-driven feature requests. Mitigation requires pre-tested code snippets, hard time budgets per section, a "parking lot" for deferred features, and backup recovery strategies. Secondary risks include JavaScript timer drift (prevented via timestamp-based calculation instead of counter decrementing) and silent failures when timer completes (prevented via multiple feedback channels with graceful degradation).

## Key Findings

### Recommended Stack

The research overwhelmingly favors a zero-dependency vanilla stack optimized for demo speed and audience comprehension. Modern JavaScript (ES6+) provides sufficient power for timer logic without framework complexity, and the single-file pattern eliminates file switching during presentation.

**Core technologies:**
- **HTML5** (latest): Structure and semantic display — zero build step, universal browser support, works via file:// protocol
- **CSS3 with Custom Properties** (latest): Styling and theming — no preprocessor needed, instant visual feedback for live CSS updates
- **Vanilla JavaScript ES6+** (ES2025): Application logic — zero dependencies, no transpilation, 2-second edit-refresh cycle

**Web APIs used:**
- **setTimeout/setInterval**: Timer countdown mechanism — standard, reliable, universally supported
- **Web Audio API (AudioContext)**: Audio notification — native tone generation without audio files
- **Notifications API** (optional): Desktop notifications — requires HTTPS and permission, make optional to avoid blocking core demo
- **CSS Custom Properties**: Dynamic theming — demonstrates modern CSS capabilities without tooling

**Why NOT frameworks:** React/Vue/Svelte require build tools (Vite/webpack), npm dependencies, and framework-specific syntax explanation. This setup time and conceptual overhead kills demo momentum and obscures core timer logic from non-technical audience members.

### Expected Features

Research into 11+ Pomodoro apps and the official Pomodoro Technique documentation reveals ONE core requirement: work for 25 minutes, then take a 5-minute break. Everything else is polish. For a 20-minute demo where planning consumes time, ruthless prioritization is essential.

**Must have (table stakes):**
- **25-minute countdown timer** — defines Pomodoro work period (estimated 5 min to implement)
- **Start/Stop button** — user control over timer (estimated 2 min to implement)
- **Visual countdown display (MM:SS)** — user needs to see time remaining (estimated 3 min to implement)
- **Timer completion signal** — user needs to know when session ends (estimated 5 min to implement)
- **5-minute break timer** — defines Pomodoro break period (estimated 3 min to implement, reuses work timer logic)

Total estimated implementation: 18 minutes of coding

**Should have (competitive differentiators, defer to post-demo):**
- Auto-cycle work/break (state complexity, 10 min)
- Desktop notifications (permission flow, 8 min)
- Progress bar visual (low value in demo, 5 min)
- Pomodoro counter (state tracking, 5 min)

**Explicitly avoid (anti-features that kill demos):**
- Task management integration (scope creep, wrong product)
- Analytics/statistics dashboard (over-engineering)
- Account system/cloud sync (massive complexity)
- Settings panels (if you need a manual, too complex)
- Music/ambient sound player (different product category)

Critical insight: "Most Pomodoro apps are over-engineered" — user reviews consistently praise minimalist timers that do basic timing exceptionally well with zero friction.

### Architecture Approach

A single-file self-contained HTML architecture is optimal for demo clarity. This structure eliminates build complexity, makes the entire codebase visible at once, and follows a simple state object + pure functions + event listeners pattern that's explainable in under 2 minutes.

**Major components:**
1. **State Management** — Single timer object holds configuration (25/5/15 min durations) and runtime state (remaining time, current mode, interval ID). Plain object, no classes.
2. **Timer Logic** — Countdown mechanics using timestamp-based calculation (not counter decrementing) to prevent drift. Functions: startTimer(), stopTimer(), switchMode(), getRemainingTime().
3. **Display Updates** — Pure functions that reflect state changes in UI. updateDisplay() formats MM:SS, updateProgress() handles visual indicators, updateModeButtons() highlights active mode.
4. **User Interactions** — Event listeners translate button clicks into state changes. Unidirectional flow: Events → State → Display.
5. **Notifications** — Optional enhancement layer for audio beeps and browser notifications with graceful fallback if permissions denied.

**Data flow:** User action → Event listener → State update → Timer calculation → Display refresh (every 1 second via setInterval)

**Key architectural pattern:** Timestamp-based timer calculation prevents drift that accumulates with counter-based approaches. Store `endTime = Date.now() + duration`, then calculate remaining on each tick rather than decrementing a counter.

**Demo build order:** Static UI (2-3 min) → State object (1-2 min) → Display updates (3-4 min) → Timer logic (4-5 min) → Mode switching (2-3 min) → Optional polish (2-3 min). Each phase delivers visible functionality and teaches one concept clearly.

### Critical Pitfalls

Research into live coding demos, JavaScript timer implementations, and technical presentations revealed these critical failure modes:

1. **Demo Time Explosion (Scope Creep)** — "Quick features" become 5-minute debugging sessions. Demo runs 30+ minutes instead of 20, audience disengages. **Prevention:** Hard scope lock to 3-4 features, time budgets per section (Planning 5 min, Core 8 min, Polish 5 min, Wrap 2 min), pre-test all code snippets, maintain "parking lot" for feature requests.

2. **Silent Failure (No User Feedback)** — Timer completes but user sees nothing (no beep, no notification). Feels broken. **Prevention:** Multiple feedback channels (visual alert + sound + notification), graceful degradation if permissions denied, show visual "COMPLETE" state clearly.

3. **JavaScript Timer Drift** — Timer set for 25:00 but actually takes 25:15 due to setInterval imprecision compounding over time. Defeats time-boxing purpose. **Prevention:** Use timestamp-based calculation (`endTime = Date.now() + duration`) not counter decrementing. Recalculate remaining time on each tick.

4. **Copy-Paste Code Theater** — Audience realizes it's all pre-written, feels deceived, loses credibility. **Prevention:** Narrate decisions during paste ("notice how this uses Date.now() because..."), make intentional bugs then debug them, show GSD in action ("GSD gave me this, here's why it's good"), acknowledge the approach openly.

5. **Technical Jargon Overload** — Terms like "event loop throttling" or "Promise rejection" alienate 60% of non-technical audience. **Prevention:** Layered explanations (concept first, technical detail optional), analogies ("timer drift is like a clock that runs slow"), focus on outcome not implementation, pause to translate technical terms.

6. **Demo Environment Sabotage** — Wi-Fi drops, battery dies, browser crashes mid-demo. **Prevention:** Full battery + charger, close all apps except browser/terminal, disable notifications/extensions, offline-capable assets (no CDN dependencies), test projector 30 min before, have backup recording ready.

## Implications for Roadmap

Based on research, the roadmap should reflect the demo constraint (20 minutes including planning) and audience context (mixed technical). Recommended structure balances visible progress with teaching moments.

### Phase 1: Static Structure
**Rationale:** Establishes visual goal and vocabulary before any code execution. Provides immediate "this is what we're building" clarity for entire audience.
**Delivers:** HTML structure (timer display, mode buttons, start/stop) + CSS styling (centered layout, readable typography). Static "25:00" displayed.
**Addresses:** Foundation for all features (from FEATURES.md table stakes)
**Avoids:** Pitfall #5 (jargon overload) — HTML/CSS universally understandable, sets context before JavaScript complexity
**Time budget:** 2-3 minutes
**Research flag:** SKIP RESEARCH — Standard HTML/CSS patterns, well-documented

### Phase 2: State Management
**Rationale:** Introduces core concept of centralized state before implementing behavior. Enables console inspection during demo for transparency.
**Delivers:** Single `timer` object with configuration (pomodoro: 25, shortBreak: 5) and runtime state placeholders
**Uses:** Vanilla JavaScript plain object pattern (from STACK.md)
**Implements:** State Management component (from ARCHITECTURE.md)
**Avoids:** Pitfall #4 (code theater) — Can show state in console, makes logic transparent
**Time budget:** 1-2 minutes
**Research flag:** SKIP RESEARCH — Simple data structure, no complex patterns

### Phase 3: Display Logic
**Rationale:** Makes timer visually functional before implementing actual countdown. Provides immediate feedback that code works.
**Delivers:** updateDisplay() function that formats time as "MM:SS", connected to DOM elements
**Addresses:** Visual countdown display (from FEATURES.md table stakes)
**Implements:** Display Updates component (from ARCHITECTURE.md)
**Avoids:** Pitfall #2 (silent failure) — Establishes visual feedback pattern early
**Time budget:** 3-4 minutes
**Research flag:** SKIP RESEARCH — Standard DOM manipulation

### Phase 4: Timer Countdown
**Rationale:** Core functionality that makes this a "timer" not just a display. Critical teaching moment for timestamp-based calculation.
**Delivers:** startTimer() and stopTimer() functions with setInterval, getRemainingTime() using timestamp calculation
**Addresses:** 25-minute countdown + Start/Stop control (from FEATURES.md table stakes)
**Uses:** setTimeout/setInterval API (from STACK.md)
**Implements:** Timer Logic component (from ARCHITECTURE.md)
**Avoids:** Pitfall #3 (timer drift) — Timestamp-based prevents accumulating errors
**Time budget:** 4-5 minutes
**Research flag:** SKIP RESEARCH — Pattern is well-established, documented in ARCHITECTURE.md

### Phase 5: Mode Switching
**Rationale:** Completes the Pomodoro cycle (work → break → work). Demonstrates event-driven architecture.
**Delivers:** switchMode() function, event listeners on mode buttons, auto-switch when timer completes
**Addresses:** 5-minute break timer (from FEATURES.md table stakes)
**Implements:** User Interactions component (from ARCHITECTURE.md)
**Avoids:** Pitfall #1 (scope creep) — Simple mode switching, not full auto-cycling which adds complexity
**Time budget:** 2-3 minutes
**Research flag:** SKIP RESEARCH — Standard event handling patterns

### Phase 6: Completion Feedback (OPTIONAL)
**Rationale:** Makes timer useful beyond demo. Progressive enhancement — core works without it.
**Delivers:** Audio beep via Web Audio API, optional browser notifications with fallback
**Addresses:** Timer completion signal (from FEATURES.md table stakes)
**Uses:** Web Audio API, Notifications API (from STACK.md)
**Implements:** Notifications component (from ARCHITECTURE.md)
**Avoids:** Pitfall #2 (silent failure) — Multiple feedback channels, Pitfall #1 (time explosion) — Skip if running late
**Time budget:** 2-3 minutes OR SKIP
**Research flag:** SKIP RESEARCH — Pattern documented in STACK.md and ARCHITECTURE.md

### Phase Ordering Rationale

- **Visual-first approach:** Static UI before behavior makes demo goal clear immediately, reduces cognitive load
- **State before logic:** Establishing state object early enables console inspection throughout demo, improves transparency
- **Feedback before complexity:** Display updates before timer logic means audience sees code work immediately, builds confidence
- **Core before polish:** Working timer (Phases 1-4) before notifications (Phase 6) ensures demo delivers even if time-constrained
- **Dependencies respected:** Each phase builds on previous — can't display timer without state, can't run timer without display, can't switch modes without timer

**Cutoff point if running late:** Phase 4 delivers a working countdown timer. Phases 5-6 can be skipped by showing pre-built final version and narrating additions.

### Research Flags

**Phases with standard patterns (SKIP additional research):**
- **Phase 1-6: ALL PHASES** — This entire project uses well-documented, mature technologies and established patterns. Research has already covered:
  - HTML/CSS structure (STACK.md, ARCHITECTURE.md)
  - JavaScript timer patterns (ARCHITECTURE.md with code examples)
  - Web Audio API usage (STACK.md with implementation)
  - Event handling (ARCHITECTURE.md)
  - All critical pitfalls and prevention (PITFALLS.md)

**No phases need deeper research during planning.** All patterns are documented in existing research files with HIGH confidence.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | All technologies verified via MDN official documentation, browser support data from Can I Use, community consensus strong |
| Features | HIGH | Based on official Pomodoro Technique documentation, 11+ app reviews, multiple implementation tutorials showing consistent patterns |
| Architecture | HIGH | Single-file pattern verified across multiple demo/tutorial sources, timestamp-based timer is established best practice to prevent drift |
| Pitfalls | HIGH | Live coding demo pitfalls from established presentation research, JavaScript timer issues verified via MDN and technical articles, mixed audience communication from professional presentation guides |

**Overall confidence:** HIGH

All research areas draw from authoritative sources (MDN, official documentation, established technical guides) and show strong consensus. No conflicting recommendations across sources. The demo context is well-studied (live coding presentations, technical demos) with clear best practices.

### Gaps to Address

Despite high overall confidence, these areas need attention during execution:

- **Demo pacing calibration** — Time estimates (5 min planning, 8 min core, 5 min polish, 2 min wrap) are based on tutorial walkthroughs, not empirical testing with this specific audience. **Handle:** Rehearse full demo at least once, adjust time budgets based on actual speaking pace. Set phone timer for 18-minute hard stop.

- **Audience technical level variance** — Mixed audience (devs, BAs, PMs) may require on-the-fly explanation depth adjustment. Research provides layering strategy but actual mix unknown. **Handle:** Poll audience at start ("Who codes regularly?" / "Who's tried Pomodoro?"), adjust jargon level based on response.

- **Copy-paste legitimacy perception** — Research shows pre-written code can feel "fake" but also that typing wastes time. Balance point is subjective. **Handle:** Be transparent about approach in introduction ("I'll paste pre-tested code so we don't watch me type, but I'll explain every decision"), make one intentional bug to debug live (builds authenticity).

- **Environment-specific technical issues** — Browser autoplay policies, notification permissions, projector resolution can vary by venue. **Handle:** Test in actual venue 30+ min before, have backup recording, know recovery strategies from PITFALLS.md reference card.

## Sources

### Primary (HIGH confidence)
- MDN Web Docs — Web Audio API, setTimeout/setInterval, Notifications API, CSS Custom Properties (official browser API documentation)
- Can I Use — Browser compatibility verification for all recommended Web APIs
- Official Pomodoro Technique documentation — Definition of table-stakes features (25 min work, 5 min break)

### Secondary (MEDIUM confidence)
- 11+ Pomodoro app reviews (2026) — Feature expectations, anti-patterns, user complaints about over-engineering
- Multiple JavaScript timer implementation tutorials (Freshman.tech, Medium, DEV Community) — Timestamp-based calculation pattern, code structure
- Live coding demo best practices — Time management, audience engagement, recovery strategies (Scott Berkun, LaunchCode, University of Washington)
- Single-file architecture patterns — Demo-optimized structure, readability guidelines (GitHub examples, DEV Community)

### Tertiary (LOW confidence, validated via cross-referencing)
- Implementation time estimates — Based on tutorial walkthroughs, not empirical testing (needs rehearsal validation)
- Mixed audience communication strategies — General presentation advice adapted to technical demo context (needs audience-specific calibration)

---
*Research completed: 2026-01-26*
*Ready for roadmap: yes*
