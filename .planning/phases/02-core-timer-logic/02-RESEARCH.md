# Phase 2: Core Timer Logic - Research

**Researched:** 2026-01-26
**Domain:** JavaScript countdown timer implementation
**Confidence:** HIGH

## Summary

Phase 2 implements the countdown mechanics that make this a functional timer. The core challenge is building an accurate countdown that doesn't drift over 25 minutes - a surprisingly common bug in naive implementations. Research confirms the solution is timestamp-based calculation: store when the timer should end, then calculate remaining time on each tick. This approach auto-corrects for any delays introduced by browser throttling or JavaScript execution.

The phase covers three requirements: TIMER-01 (countdown logic), TIMER-02 (start/stop/reset controls), and TIMER-04 (completion indicator). All use well-established patterns with universal browser support. The critical insight is that `setInterval` guarantees execution no faster than the specified delay, but makes no promise about precision - callbacks can be delayed by browser throttling (especially in background tabs) or JavaScript execution time.

**Primary recommendation:** Use timestamp-based calculation (`endTime - Date.now()`) instead of counter decrementing (`seconds--`) to prevent cumulative drift.

## Standard Stack

Phase 2 uses vanilla JavaScript with standard Web APIs. No additional libraries needed.

### Core
| API | Purpose | Why Standard |
|-----|---------|--------------|
| `setInterval()` | Call tick function every second | Universal support, simple pattern |
| `clearInterval()` | Stop the countdown | Required cleanup, prevents memory leaks |
| `Date.now()` | Get current timestamp in milliseconds | High precision, universal support |
| `Math.floor()` | Convert milliseconds to whole seconds | Standard math operation |
| `Math.max()` | Prevent negative time display | Guard against overshoot |

### Supporting
| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| Nullish coalescing (`??=`) | Guard against duplicate intervals | Start button clicked multiple times |
| Template literals | Format MM:SS display | String interpolation for time |
| `padStart()` | Zero-pad single digits | "5:03" not "5:3" |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| `setInterval` | Recursive `setTimeout` | More control over timing but more complex |
| `setInterval` | `requestAnimationFrame` | Better for animation, overkill for 1-second updates |
| `Date.now()` | `performance.now()` | Higher precision, but milliseconds sufficient for timer |

**No additional installation needed.** All APIs are built into every browser.

## Architecture Patterns

### Recommended State Structure
```javascript
// Phase 1 provides this structure, Phase 2 adds runtime state
const timer = {
  // Configuration (from Phase 1)
  durations: {
    pomodoro: 25 * 60,    // seconds
    shortBreak: 5 * 60
  },

  // Runtime state (Phase 2 adds these)
  endTime: null,          // Timestamp when timer should complete
  intervalId: null,       // Reference for cleanup
  isRunning: false,       // Current state
  remainingSeconds: 25 * 60  // Current remaining time
};
```

### Pattern 1: Timestamp-Based Countdown (CRITICAL)
**What:** Store end timestamp, calculate remaining time on each tick
**When to use:** Always for countdowns longer than a few seconds
**Why:** Prevents drift from setInterval imprecision and background throttling

```javascript
// Source: MDN setInterval documentation + established best practice
function startTimer() {
  if (timer.intervalId) return; // Prevent duplicate intervals

  // Calculate when timer should end
  timer.endTime = Date.now() + (timer.remainingSeconds * 1000);
  timer.isRunning = true;

  timer.intervalId = setInterval(() => {
    const now = Date.now();
    const remaining = Math.max(0, timer.endTime - now);

    timer.remainingSeconds = Math.floor(remaining / 1000);
    updateDisplay(); // From Phase 1

    if (remaining <= 0) {
      handleComplete();
    }
  }, 1000);
}
```

### Pattern 2: Clean Interval Stop
**What:** Clear interval and reset state atomically
**When to use:** Stop/pause button, timer completion, mode switch

```javascript
// Source: MDN clearInterval documentation
function stopTimer() {
  if (timer.intervalId) {
    clearInterval(timer.intervalId);
    timer.intervalId = null;
  }
  timer.isRunning = false;
  // remainingSeconds already has correct value from last tick
}
```

### Pattern 3: Reset to Initial State
**What:** Stop timer and restore to starting duration
**When to use:** Reset button, mode switch

```javascript
function resetTimer() {
  stopTimer();
  timer.remainingSeconds = timer.durations[timer.mode]; // mode from Phase 1
  timer.endTime = null;
  updateDisplay();
}
```

### Pattern 4: Completion Handler
**What:** Clean stop plus visual/audio feedback
**When to use:** Timer reaches 00:00

```javascript
function handleComplete() {
  stopTimer();
  timer.remainingSeconds = 0;
  updateDisplay();
  showCompletionState(); // Visual indicator
  // Audio notification added in Phase 3 or later
}
```

### Anti-Patterns to Avoid

- **Counter decrementing:** `seconds--` accumulates drift over time. After 25 minutes, can be 10-30 seconds off.

  ```javascript
  // BAD: Drifts over time
  setInterval(() => {
    seconds--;  // Assumes exactly 1000ms passed - WRONG
    updateDisplay(seconds);
  }, 1000);
  ```

- **Multiple active intervals:** Clicking Start twice creates two intervals counting down simultaneously.

  ```javascript
  // BAD: No guard against duplicate intervals
  function startTimer() {
    setInterval(tick, 1000); // Each click adds another interval!
  }
  ```

- **Forgetting to clear on stop:** Memory leak, timer keeps running invisibly.

- **Storing both start and end time:** Redundant - store one timestamp plus remaining time.

## Don't Hand-Roll

Problems that look simple but have edge cases:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Accurate timing | Counter decrement | Timestamp calculation | setInterval can be delayed 4ms-1000ms+ |
| Time formatting | Manual string concatenation | `padStart(2, '0')` | "5:03" vs "5:3", handles edge cases |
| Interval cleanup | Trust garbage collection | Explicit `clearInterval()` | Intervals run forever until cleared |
| Negative time guard | Hope it never happens | `Math.max(0, remaining)` | Race conditions can show -1 seconds |

**Key insight:** Browser timing is non-deterministic. MDN explicitly states: "the actual amount of time that elapses between calls to the callback may be longer than the given delay."

## Common Pitfalls

### Pitfall 1: Timer Drift Over 25 Minutes
**What goes wrong:** Timer set for 25:00 completes at 25:15 or 24:45
**Why it happens:** `setInterval(fn, 1000)` doesn't guarantee exactly 1000ms between calls. Browser throttling, JavaScript execution, and event loop delays accumulate.
**How to avoid:** Timestamp-based calculation (Pattern 1 above)
**Warning signs:** Timer seems slightly fast or slow, inconsistent completion times

### Pitfall 2: Background Tab Throttling
**What goes wrong:** Switch to another tab, come back, timer shows wrong time
**Why it happens:** Browsers throttle timers in inactive tabs:
  - Chrome: Once per minute for tabs inactive >5min (Chrome 88+)
  - Firefox: 1 second minimum for inactive tabs
  - Mobile browsers: May suspend entirely
**How to avoid:** Timestamp-based calculation auto-corrects when tab becomes active
**Warning signs:** Timer "catches up" suddenly when returning to tab

### Pitfall 3: Multiple Intervals Running
**What goes wrong:** Timer counts down twice as fast, erratic behavior
**Why it happens:** Start button clicked multiple times without guard
**How to avoid:** Check `if (timer.intervalId) return;` at start of startTimer()
**Warning signs:** Timer speeds up after clicking Start again

### Pitfall 4: Memory Leak from Uncleaned Intervals
**What goes wrong:** Browser slows down after many start/stop cycles
**Why it happens:** Old intervals not cleared before creating new ones
**How to avoid:** Always `clearInterval(timer.intervalId)` before setting new interval
**Warning signs:** DevTools shows increasing memory usage

### Pitfall 5: Negative Time Display
**What goes wrong:** Timer shows "00:-01" or "00:-02"
**Why it happens:** Tick fires slightly after timer should have ended
**How to avoid:** `Math.max(0, remaining)` and check `if (remaining <= 0)` before decrementing
**Warning signs:** Brief flash of negative time before completion handler fires

### Pitfall 6: Pause/Resume Bug
**What goes wrong:** Pause at 20:00, wait 5 minutes, resume - shows 15:00 instead of 20:00
**Why it happens:** Resume recalculates from original endTime instead of paused remainingSeconds
**How to avoid:** On pause, store remainingSeconds. On resume, calculate new endTime from remainingSeconds.
**Warning signs:** Timer "loses" time during pause

## Code Examples

### Complete startTimer Implementation
```javascript
// Source: Verified pattern from MDN + ARCHITECTURE.md research
function startTimer() {
  // Guard: prevent multiple intervals
  if (timer.intervalId !== null) return;

  // Calculate end timestamp from current remaining time
  timer.endTime = Date.now() + (timer.remainingSeconds * 1000);
  timer.isRunning = true;

  // Start the tick cycle
  timer.intervalId = setInterval(() => {
    const now = Date.now();
    const remainingMs = Math.max(0, timer.endTime - now);

    timer.remainingSeconds = Math.floor(remainingMs / 1000);
    updateDisplay();

    // Check for completion
    if (remainingMs <= 0) {
      handleComplete();
    }
  }, 1000);

  // Update UI to show running state
  updateStartStopButton();
}
```

### Complete stopTimer Implementation
```javascript
// Source: MDN clearInterval + research best practices
function stopTimer() {
  if (timer.intervalId !== null) {
    clearInterval(timer.intervalId);
    timer.intervalId = null;
  }
  timer.isRunning = false;
  updateStartStopButton();
}
```

### Complete resetTimer Implementation
```javascript
// Source: Architecture patterns from research
function resetTimer() {
  stopTimer();
  timer.remainingSeconds = timer.durations[timer.mode];
  timer.endTime = null;
  updateDisplay();
}
```

### Complete handleComplete Implementation
```javascript
// Source: Research requirements TIMER-04
function handleComplete() {
  stopTimer();
  timer.remainingSeconds = 0;
  updateDisplay();

  // Visual completion indicator
  const display = document.getElementById('timer-display');
  display.classList.add('complete');

  // Optional: Change display text
  // display.textContent = "Done!";
}
```

### Time Formatting Helper (if not in Phase 1)
```javascript
// Source: Standard JavaScript string method
function formatTime(totalSeconds) {
  const minutes = Math.floor(totalSeconds / 60);
  const seconds = totalSeconds % 60;
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
}
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Counter decrement | Timestamp calculation | Always was better, but tutorials often teach old way | Prevents 10-30 second drift over 25 min |
| setInterval only | requestAnimationFrame for UI | ~2015 | Better for animations, overkill for timers |
| Unchecked intervals | Guard against duplicates | Always was needed | Prevents erratic behavior |

**Browser throttling intensified:**
- Chrome 88 (Jan 2021): Intensive throttling for background tabs - timers only fire once per minute after 5 minutes inactive
- This makes timestamp-based calculation even more critical

## Open Questions

None for this phase. The patterns are well-established and documented.

All timer logic follows standard JavaScript patterns with universal browser support. No experimental features, no edge cases unresolved.

## Sources

### Primary (HIGH confidence)
- [MDN: setInterval](https://developer.mozilla.org/en-US/docs/Web/API/Window/setInterval) - Timing limitations, nested interval enforcement
- [MDN: clearInterval](https://developer.mozilla.org/en-US/docs/Web/API/Window/clearInterval) - Proper cleanup patterns
- [MDN: setTimeout - Reasons for delays](https://developer.mozilla.org/en-US/docs/Web/API/Window/setTimeout#reasons_for_delays_longer_than_specified) - Background tab throttling specifics

### Secondary (MEDIUM confidence)
- Project ARCHITECTURE.md - Timer logic patterns, state management structure
- Project PITFALLS.md - Timer drift prevention, demo-specific warnings
- Project STACK.md - Technology decisions, vanilla JS rationale

### Verified Patterns
All code examples follow patterns verified against:
1. MDN official documentation
2. Prior project research (ARCHITECTURE.md, PITFALLS.md)
3. Standard JavaScript best practices

## Metadata

**Confidence breakdown:**
- Timestamp-based timing: HIGH - MDN explicitly warns about setInterval imprecision
- Start/stop/reset patterns: HIGH - Standard JavaScript, universal browser support
- Completion handling: HIGH - Simple state change, well-documented
- Background throttling: HIGH - MDN documents specific browser behaviors

**Research date:** 2026-01-26
**Valid until:** Indefinite - these are stable Web APIs unchanged since ES5
