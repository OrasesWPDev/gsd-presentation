# Architecture Patterns: Minimal Pomodoro Timer

**Domain:** Browser-based Pomodoro Timer
**Researched:** 2026-01-26
**Context:** GSD training demo - optimized for demo clarity and teaching
**Confidence:** HIGH

## Executive Summary

For a demo-focused Pomodoro timer, a **single-file self-contained HTML architecture** is optimal. This structure eliminates build complexity, makes the entire codebase visible at once, and allows the demo to focus on core concepts rather than tooling. The architecture follows a simple **state object + pure functions + event listeners** pattern that's easy to explain in under 2 minutes.

## Recommended Architecture: Single-File Self-Contained

### File Structure

**Option 1: True Single File (RECOMMENDED for demo)**
```
pomodoro.html          # Everything in one file
  ├─ <style>          # CSS embedded in head
  ├─ <body>           # HTML structure
  └─ <script>         # JavaScript at bottom
```

**Option 2: Simple Multi-File (if demo needs to show separation)**
```
index.html            # Structure only
styles.css            # Presentation only
script.js             # Behavior only
```

**Recommendation:** Use Option 1 (single file) unless the demo specifically needs to teach separation of concerns. Single-file provides:
- Open in browser immediately, no server needed
- Complete portability (can email, embed, share as single artifact)
- Entire codebase visible without file switching
- Lower barrier to understanding for non-experts

## Component Breakdown

### Component 1: State Management (The Brain)

**Responsibility:** Single source of truth for timer configuration and current state

```javascript
const timer = {
  // Configuration
  pomodoro: 25,           // Work session length (minutes)
  shortBreak: 5,          // Short break length
  longBreak: 15,          // Long break after 4 sessions
  longBreakInterval: 4,   // Sessions before long break

  // Runtime state
  sessions: 0,            // Completed pomodoro count
  mode: 'pomodoro',       // Current mode
  remainingTime: null,    // Timestamp for countdown
  interval: null          // setInterval reference
}
```

**Why this structure:**
- Plain object, no classes (easier to explain)
- All values in one place (no hunting for state)
- Configuration vs runtime state clearly separated
- Can be logged/inspected in console during demo

### Component 2: Timer Logic (The Engine)

**Responsibility:** Countdown mechanics and mode transitions

**Key Functions:**
```javascript
startTimer()            // Begin countdown using setInterval
stopTimer()             // Pause countdown, preserve remainingTime
switchMode(mode)        // Transition between pomodoro/break modes
getRemainingTime()      // Calculate time left from timestamp
```

**Implementation Pattern:**
- Use `setInterval()` with 1000ms (1 second) intervals
- Store end timestamp, not current seconds (prevents drift)
- Clear interval on stop/complete to prevent memory leaks
- Automatically switch modes when timer reaches zero

**Anti-Pattern to Avoid:**
- DON'T decrement a counter (accumulates drift over time)
- DON'T use `setTimeout()` recursively (harder to control)
- DON'T store both start and end times (one timestamp sufficient)

### Component 3: Display Updates (The Face)

**Responsibility:** Reflect state changes in the UI

**Key Functions:**
```javascript
updateDisplay()         // Refresh countdown text (MM:SS format)
updateProgress()        // Update visual progress bar/circle
updateModeButtons()     // Highlight active mode button
updateDocumentTitle()   // Show timer in browser tab
```

**Data Flow:**
```
timer.remainingTime changed
  → getRemainingTime() calculates minutes/seconds
  → updateDisplay() formats as "25:00"
  → DOM textContent updated
```

**Why separate display functions:**
- Each function has single responsibility
- Can update independently (e.g., progress without time)
- Easy to add/remove UI features without touching logic
- Clear demo narrative: "Now let's make it visible"

### Component 4: User Interactions (The Controls)

**Responsibility:** Translate user actions into state changes

**Event Handlers:**
```javascript
// Mode selection buttons
buttonPomodoro.addEventListener('click', () => switchMode('pomodoro'))
buttonShortBreak.addEventListener('click', () => switchMode('shortBreak'))
buttonLongBreak.addEventListener('click', () => switchMode('longBreak'))

// Start/Stop toggle
buttonToggle.addEventListener('click', () => {
  if (timer.interval) stopTimer()
  else startTimer()
})
```

**Pattern:**
- Event listeners call state-changing functions
- Functions update state object
- State changes trigger display updates
- Unidirectional data flow: Events → State → Display

### Component 5: Notifications (The Alerts)

**Responsibility:** Alert user when timer completes

**Optional Features:**
```javascript
playSound()             // Audio.play() for completion chime
showNotification()      // Browser Notification API
changeTabTitle()        // Browser tab shows "Break time!"
```

**Progressive Enhancement:**
- Core timer works without notifications
- Add notifications as enhancement in demo
- Request permission elegantly (don't block core functionality)
- Provide visual fallback if notifications denied

## Data Flow Architecture

### Unidirectional Event-Driven Flow

```
┌──────────────────────────────────────────────────┐
│                                                  │
│  USER ACTION (button click)                     │
│         │                                        │
│         ▼                                        │
│  EVENT LISTENER (addEventListener)              │
│         │                                        │
│         ▼                                        │
│  STATE UPDATE (modify timer object)             │
│         │                                        │
│         ▼                                        │
│  TIMER LOGIC (calculate remaining time)         │
│         │                                        │
│         ▼                                        │
│  DISPLAY UPDATE (refresh DOM)                   │
│         │                                        │
│         └─────────────────────────────────────┐  │
│                                               │  │
│  (cycle repeats every 1 second via setInterval) │
│                                                  │
└──────────────────────────────────────────────────┘
```

### State-Display Synchronization

**Key Principle:** Display is derived from state, never independent

```javascript
// GOOD: State is source of truth
function startTimer() {
  timer.remainingTime = Date.now() + (minutes * 60 * 1000)
  timer.interval = setInterval(() => {
    const timeLeft = getRemainingTime()
    updateDisplay(timeLeft)  // Derived from state
  }, 1000)
}

// BAD: Display and state can diverge
let seconds = 60
setInterval(() => {
  seconds--  // State here
  display.textContent = seconds  // State also here (can drift)
}, 1000)
```

## Suggested Build Order for Demo

This order maximizes demo narrative flow and teaching clarity.

### Phase 1: Static Structure (HTML + CSS)
**Time:** 2-3 minutes of demo
**What to build:**
- HTML structure: timer display, mode buttons, start/stop button
- CSS styling: center layout, circular timer aesthetic
- Static text: "25:00" hardcoded

**Demo narrative:** "Here's what we're building - a clean Pomodoro timer interface"

**Teaching value:**
- Shows end goal visually
- Establishes vocabulary (pomodoro, break, modes)
- Sets expectations for functionality

### Phase 2: State Object
**Time:** 1-2 minutes of demo
**What to build:**
```javascript
const timer = {
  pomodoro: 25,
  shortBreak: 5,
  longBreak: 15,
  sessions: 0
}
```

**Demo narrative:** "All our configuration lives in one place"

**Teaching value:**
- Introduces state management concept
- Shows how to organize related data
- Can console.log() to inspect state

### Phase 3: Display Updates
**Time:** 3-4 minutes of demo
**What to build:**
- Select DOM elements
- `updateDisplay()` function
- Format time as "MM:SS"
- Call once to show it works

**Demo narrative:** "Let's make our timer show the right time"

**Teaching value:**
- DOM manipulation
- String formatting
- Pure function (input → output, no side effects)
- Immediate visual feedback in browser

### Phase 4: Timer Logic
**Time:** 4-5 minutes of demo
**What to build:**
- `startTimer()` function
- `setInterval()` pattern
- `getRemainingTime()` calculation
- `stopTimer()` function

**Demo narrative:** "Now let's make it count down"

**Teaching value:**
- JavaScript timing functions
- Timestamp-based calculation (prevents drift)
- setInterval/clearInterval pattern
- Can test immediately by clicking start

### Phase 5: Mode Switching
**Time:** 2-3 minutes of demo
**What to build:**
- `switchMode()` function
- Event listeners on mode buttons
- Update display when mode changes
- Auto-switch when timer completes

**Demo narrative:** "Let's add work/break mode switching"

**Teaching value:**
- Event-driven architecture
- State transitions
- Automatic behavior (timer complete → switch mode)

### Phase 6: Polish & Enhancements
**Time:** 2-3 minutes of demo
**What to build:**
- Progress circle animation
- Browser tab title updates
- Completion sound
- Browser notifications (if time permits)

**Demo narrative:** "Let's add some nice-to-haves"

**Teaching value:**
- Progressive enhancement
- Browser APIs (Audio, Notifications)
- CSS animations
- These are optional (core works without them)

**Total demo time:** 15-20 minutes for complete timer

## Technical Implementation Details

### DOM Selection Strategy

```javascript
// Select all elements once at module load
const elements = {
  display: document.getElementById('timer-display'),
  buttonStart: document.getElementById('btn-start'),
  buttonPomodoro: document.getElementById('btn-pomodoro'),
  buttonShortBreak: document.getElementById('btn-short-break'),
  buttonLongBreak: document.getElementById('btn-long-break')
}
```

**Why:**
- Avoids repeated querySelector calls (performance)
- Clear inventory of all UI elements
- Easy to add new elements during demo
- Can comment out lines during incremental demo

### Time Calculation Pattern

```javascript
function getRemainingTime() {
  const now = Date.now()
  const difference = timer.remainingTime - now

  if (difference <= 0) {
    stopTimer()
    handleTimerComplete()
    return { minutes: 0, seconds: 0 }
  }

  const totalSeconds = Math.floor(difference / 1000)
  const minutes = Math.floor(totalSeconds / 60)
  const seconds = totalSeconds % 60

  return { minutes, seconds }
}
```

**Why this approach:**
- Uses timestamps, not counters (no drift accumulation)
- Handles timer completion naturally (difference <= 0)
- Returns structured data (easy to format)
- Pure function (testable, predictable)

### Mode Transition Pattern

```javascript
function switchMode(mode) {
  timer.mode = mode
  timer.remainingTime = null

  if (timer.interval) {
    stopTimer()
  }

  const minutes = timer[mode]  // Lookup: timer.pomodoro, etc.
  updateDisplay({ minutes, seconds: 0 })
  updateModeButtons(mode)
}
```

**Why this pattern:**
- Stops current timer when switching (prevents confusion)
- Resets display to new mode duration
- Uses bracket notation for dynamic property access
- Single function handles all mode types

### Session Counter Pattern

```javascript
function handleTimerComplete() {
  if (timer.mode === 'pomodoro') {
    timer.sessions++

    // Every 4 sessions, take a long break
    if (timer.sessions % timer.longBreakInterval === 0) {
      switchMode('longBreak')
    } else {
      switchMode('shortBreak')
    }
  } else {
    // Break complete, back to work
    switchMode('pomodoro')
  }

  startTimer()  // Auto-start next session
}
```

**Why this pattern:**
- Implements Pomodoro technique correctly
- Sessions only increment on work completion (not breaks)
- Automatic mode switching (no user intervention needed)
- Auto-start keeps flow going (user can stop if needed)

## Scalability Considerations

### At Demo Scale (single user, local file)

**Current architecture is perfect:**
- No server needed
- No database needed
- No authentication needed
- No build step needed

### If Extended to PWA (offline-first mobile app)

**Additions needed:**
```
├─ manifest.json           # PWA metadata
├─ service-worker.js       # Offline capability
└─ localStorage integration # Persist timer state
```

**Architecture changes:**
- Add `saveState()` / `loadState()` functions
- Store `timer` object in localStorage
- Register service worker for offline access
- No changes to core timer logic

### If Extended to Multi-User (timer sharing)

**Additions needed:**
```
├─ WebSocket connection    # Real-time sync
├─ Backend API            # Store shared timers
└─ User authentication    # Identify timer owners
```

**Architecture changes:**
- State becomes server-authoritative
- Local state is cache of server state
- Add sync functions
- Core timer logic unchanged (still works locally)

## Anti-Patterns to Avoid

### Anti-Pattern 1: Class-Based Architecture

**What:**
```javascript
class PomodoroTimer {
  constructor() { /* ... */ }
  start() { /* ... */ }
  stop() { /* ... */ }
}
```

**Why bad for demos:**
- Requires explaining `this` context
- Requires explaining classes/constructors
- More complex than needed
- Harder to inspect state (need `this`)

**Instead:** Plain object + pure functions (easier to understand)

### Anti-Pattern 2: Framework Dependence

**What:**
```javascript
import React from 'react'
import { useState, useEffect } from 'react'
// ... 50 lines of setup ...
```

**Why bad for demos:**
- Requires build step (Webpack, Vite, etc.)
- Can't open HTML file directly
- More to explain (components, hooks, JSX)
- Obscures core timer logic

**Instead:** Vanilla JavaScript (nothing to install or configure)

### Anti-Pattern 3: Counter-Based Timer

**What:**
```javascript
let seconds = 1500
setInterval(() => {
  seconds--
  updateDisplay(seconds)
}, 1000)
```

**Why bad:**
- Accumulates drift (setInterval isn't precise)
- Pausing/resuming is complex
- Can't calculate progress accurately
- State is in closure, hard to inspect

**Instead:** Timestamp-based with end time calculation

### Anti-Pattern 4: Multiple State Sources

**What:**
```javascript
let currentMode = 'pomodoro'  // State here
let remainingSeconds = 1500   // State here
let sessionCount = 0          // State here
let isRunning = false         // State here
```

**Why bad:**
- State scattered across file
- Hard to see full state at once
- Can't save/restore easily
- Difficult to debug

**Instead:** Single `timer` object with all state

### Anti-Pattern 5: Inline Event Handlers

**What:**
```html
<button onclick="startTimer()">Start</button>
```

**Why bad:**
- Mixes HTML and JavaScript
- Harder to manage in single file
- Global function pollution
- Not the modern pattern

**Instead:** `addEventListener` in JavaScript section

## Browser Compatibility

**Target:** Modern evergreen browsers (Chrome, Firefox, Safari, Edge)

**Required APIs:**
- `setInterval()` / `clearInterval()` (universal support)
- `Date.now()` (universal support)
- `addEventListener()` (universal support)
- `querySelector()` / `getElementById()` (universal support)

**Optional APIs (with fallbacks):**
- `Audio()` constructor (for completion sound)
  - Fallback: Visual-only indicator
- `Notification` API (for browser notifications)
  - Fallback: Tab title update
  - Requires user permission
- CSS custom properties (for theming)
  - Fallback: Standard CSS values

**No polyfills needed** for core functionality.

## File Size Budget

For demo clarity, keep total file size minimal:

| Component | Target Size | Notes |
|-----------|-------------|-------|
| HTML | < 1 KB | Minimal structure |
| CSS | < 2 KB | Simple styling |
| JavaScript | < 5 KB | Core logic only |
| **Total** | **< 8 KB** | Loads instantly |

**Why this matters:**
- Entire file fits on one screen during demo
- Easy to read through in < 5 minutes
- No scrolling fatigue
- Focus on concepts, not code volume

## Code Style for Demo Clarity

### Naming Conventions

```javascript
// Variables: Descriptive camelCase
const remainingTime = getRemainingTime()
const currentMode = timer.mode

// Functions: Verb + noun
function startTimer() { }
function updateDisplay() { }
function switchMode() { }

// Constants: UPPER_CASE for true constants
const MILLISECONDS_PER_SECOND = 1000
const MINUTES_PER_POMODORO = 25
```

### Function Length

**Target:** 10-15 lines maximum per function

**Why:**
- Fits on screen during demo
- Single responsibility obvious
- Easy to explain in 1-2 sentences
- Can show entire function without scrolling

### Comments Strategy

**Minimal inline comments** (code should be self-explanatory)

```javascript
// GOOD: Comment explains WHY
// Use timestamps to prevent drift from setInterval imprecision
const endTime = Date.now() + (minutes * 60 * 1000)

// BAD: Comment explains WHAT (code already says this)
// Set the end time
const endTime = Date.now() + (minutes * 60 * 1000)
```

**Comment sections for demo navigation:**

```javascript
// ============================================
// STATE MANAGEMENT
// ============================================

const timer = { /* ... */ }

// ============================================
// TIMER LOGIC
// ============================================

function startTimer() { /* ... */ }
```

### Formatting for Projection

**Considerations when showing code on screen:**
- Larger font size (16-18pt for projector)
- High contrast (dark background, light text)
- Short lines (< 80 characters for visibility)
- Generous whitespace (breathing room between sections)

## Testing Strategy (Optional for Demo)

**Manual testing approach:**

1. Start timer → counts down correctly
2. Pause timer → stops at current time
3. Resume timer → continues from paused time
4. Switch modes → displays correct duration
5. Complete timer → switches mode automatically
6. Complete 4 pomodoros → triggers long break

**Console inspection:**
```javascript
// During demo, show state in console
console.log('Current state:', timer)
console.log('Remaining time:', getRemainingTime())
```

**No unit tests needed** - demo focuses on functionality, not test-driven development

## Summary: Why This Architecture Works for Demos

| Requirement | How Architecture Addresses It |
|-------------|------------------------------|
| Easy to understand | Plain objects + pure functions, no classes/frameworks |
| Quick to show | Single file, open in browser, no build step |
| Readable by non-experts | Descriptive names, short functions, clear structure |
| No complexity | No bundlers, no dependencies, no tooling |
| Runs immediately | File:// protocol works, no server needed |
| Demo-friendly build order | Each phase adds visible functionality |
| Clear narrative | Components map to concepts: State, Logic, Display, Events |
| Inspectable | Can console.log state during demo |
| Extensible | Clean separation allows adding features incrementally |

## Build Order Summary (Quick Reference)

1. **Static UI** → Visual structure (HTML + CSS)
2. **State Object** → Data organization
3. **Display Updates** → DOM manipulation
4. **Timer Logic** → Countdown mechanics
5. **Mode Switching** → Event handling
6. **Enhancements** → Progressive features

Each phase builds on previous, provides immediate feedback, and teaches one concept clearly.

## Confidence Assessment

| Aspect | Confidence | Reason |
|--------|------------|--------|
| Architecture Pattern | HIGH | Single-file pattern verified across multiple sources, standard for demos |
| Component Structure | HIGH | State + logic + display + events pattern universal in timer apps |
| Build Order | HIGH | Incremental approach matches tutorial sequences found in research |
| Data Flow | HIGH | Unidirectional event-driven flow is established best practice |
| Demo Suitability | HIGH | All recommendations specifically optimized for teaching/demo context |

## Sources

### Pomodoro Timer Implementations
- [Freshman.tech - How to build a Pomodoro Timer App with JavaScript](https://freshman.tech/pomodoro-timer/)
- [Building a Modern Pomodoro Timer with Vanilla JavaScript: A Complete Guide](https://medium.com/@francesco.saviano87/building-a-modern-pomodoro-timer-with-vanilla-javascript-a-complete-guide-48f7fd96cad9)
- [DEV Community - Creating a Simple Timer App with JavaScript](https://dev.to/sbre/creating-a-simple-timer-app-with-javascript-55b9)

### Single-File Architecture Patterns
- [HTML, CSS, and JavaScript in One File: A Complete 2026 Guide](https://copyprogramming.com/howto/how-to-put-html-css-and-js-in-one-single-file)
- [DEV Community - Single-File JavaScript Modules](https://dev.to/tythos/single-file-javascript-modules-7aj)
- [GitHub - nappysoft/self-contained](https://github.com/nappysoft/self-contained)

### JavaScript Best Practices
- [MDN - Guidelines for writing JavaScript code examples](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Code_style_guide/JavaScript)
- [MDN - Live samples structure](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Page_structures/Live_samples)
- [JavaScript Performance Optimization: Best Practices for 2026](https://www.landskill.com/blog/javascript-performance-optimization/)

### Timer Implementation Patterns
- [W3Schools - JavaScript Timing Events](https://www.w3schools.com/js/js_timing.asp)
- [SitePoint - Build a Countdown Timer in Just 18 Lines of JavaScript](https://www.sitepoint.com/build-javascript-countdown-timer-no-dependencies/)
- [Educative - How to create a countdown timer using JavaScript](https://www.educative.io/answers/how-to-create-a-countdown-timer-using-javascript)
