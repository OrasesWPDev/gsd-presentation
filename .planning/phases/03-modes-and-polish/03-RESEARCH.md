# Phase 3: Modes and Polish - Research

**Researched:** 2026-01-26
**Domain:** Mode switching, session tracking, Pomodoro workflow completion
**Confidence:** HIGH

## Summary

Phase 3 adds the final behavioral layer to the Pomodoro timer: mode switching between work and break sessions, and session counting to track completed Pomodoros. This phase transforms a simple countdown timer into a proper Pomodoro technique tool.

The implementation patterns are well-established in the vanilla JavaScript ecosystem. The standard approach uses:
1. A mode state property that switches between "pomodoro" and "shortBreak"
2. Mode buttons with `data-mode` attributes for clean event delegation
3. A session counter that increments when a Pomodoro completes (not when a break completes)
4. Automatic mode transitions driven by completion detection from Phase 2

**Primary recommendation:** Extend the existing `timer` state object with `mode` and `sessions` properties. Use `data-mode` attributes on buttons for clean mode switching. Increment sessions only when Pomodoro mode completes.

## Standard Stack

The phase builds on the existing vanilla JS stack from Phases 1-2. No new libraries needed.

### Core
| Technology | Version | Purpose | Why Standard |
|------------|---------|---------|--------------|
| Vanilla JavaScript | ES6+ | Mode switching logic, session tracking | Continues Phase 1-2 approach, no dependencies |
| HTML data attributes | HTML5 | `data-mode` for mode button identification | Clean separation of data from styling |
| CSS Custom Properties | CSS3 | Mode-specific color theming | Dynamic theme switching without JS |

### Supporting
| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| Event delegation | Single listener for all mode buttons | When multiple buttons share similar behavior |
| State object extension | Add `mode` and `sessions` to existing `timer` object | Keeps all state centralized |
| Conditional UI updates | Different colors/styles per mode | Visual feedback for current mode |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| `data-mode` attributes | Separate click handlers per button | More code, harder to extend for long break |
| Single state object | Separate variables | Harder to inspect, save, or debug |
| CSS custom properties for theming | JavaScript style manipulation | JS approach is more verbose, less performant |

**Installation:**
```bash
# No installation needed - continues Phase 1-2 vanilla approach
```

## Architecture Patterns

### Recommended State Extension

Extend the Phase 2 timer state object with mode and session properties:

```javascript
const timer = {
  // From Phase 1 - Configuration
  pomodoro: 25,
  shortBreak: 5,

  // From Phase 2 - Runtime state
  remainingTime: null,
  interval: null,

  // Phase 3 additions
  mode: 'pomodoro',    // Current mode: 'pomodoro' | 'shortBreak'
  sessions: 0          // Completed Pomodoro count
};
```

### Pattern 1: Mode Switching with Data Attributes

**What:** Use `data-mode` attributes on buttons for clean mode identification
**When to use:** When multiple buttons trigger similar behavior with different parameters

```html
<!-- HTML structure for mode buttons -->
<div class="mode-buttons">
  <button class="mode-btn active" data-mode="pomodoro">Pomodoro</button>
  <button class="mode-btn" data-mode="shortBreak">Short Break</button>
</div>
```

```javascript
// Source: freshman.tech/pomodoro-timer pattern
function switchMode(mode) {
  timer.mode = mode;
  timer.remainingTime = timer[mode] * 60; // Lookup duration dynamically

  // Update UI to show active mode
  document.querySelectorAll('.mode-btn').forEach(btn => {
    btn.classList.remove('active');
  });
  document.querySelector(`[data-mode="${mode}"]`).classList.add('active');

  // Update display
  updateDisplay();

  // Optional: Change background color per mode
  document.body.style.backgroundColor = `var(--${mode}-color)`;
}

// Event listener using data attributes
document.querySelectorAll('.mode-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    const mode = btn.dataset.mode;
    switchMode(mode);
  });
});
```

### Pattern 2: Session Counter Increment on Pomodoro Completion

**What:** Only increment sessions when a Pomodoro (work session) completes, not breaks
**When to use:** When timer reaches 00:00 in Pomodoro mode

```javascript
// Source: freshman.tech/pomodoro-timer pattern
function handleTimerComplete() {
  // Only count completed Pomodoros, not breaks
  if (timer.mode === 'pomodoro') {
    timer.sessions++;
    updateSessionDisplay();
  }

  // Auto-switch to opposite mode
  if (timer.mode === 'pomodoro') {
    switchMode('shortBreak');
  } else {
    switchMode('pomodoro');
  }
}

function updateSessionDisplay() {
  const display = document.getElementById('session-count');
  display.textContent = `Pomodoros completed: ${timer.sessions}`;
}
```

### Pattern 3: Mode Switching Preserves Running State

**What:** When user clicks a mode button while timer is running, start new mode immediately
**When to use:** Per success criteria - "Mode switching preserves timer state (if running, keeps running with new duration)"

```javascript
function switchMode(mode) {
  const wasRunning = timer.interval !== null;

  // Stop current timer if running
  if (wasRunning) {
    stopTimer();
  }

  // Update mode and duration
  timer.mode = mode;
  timer.remainingTime = timer[mode] * 60;

  // Update UI
  updateModeButtons(mode);
  updateDisplay();

  // If was running, start new timer
  if (wasRunning) {
    startTimer();
  }
}
```

### Anti-Patterns to Avoid

- **Separate duration variables:** Don't use `pomodoroDuration`, `breakDuration` as separate variables. Use the timer object's named properties (`timer.pomodoro`, `timer.shortBreak`) with dynamic lookup via `timer[mode]`.

- **Incrementing sessions on any completion:** Only increment when Pomodoro mode completes. Break completions should not count toward session total.

- **Hard-coded mode checks:** Use `data-mode` attributes and dynamic property access instead of `if (clickedButton.id === 'pomodoroBtn')`.

## Don't Hand-Roll

Problems with existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Mode state management | Custom state machine | Simple string property + switch/if | Timer only has 2-3 modes, state machine is overkill |
| Session persistence | Custom serialization | localStorage with JSON.stringify | One line to save, one to restore |
| Mode-specific styling | JavaScript style manipulation | CSS custom properties per mode | Cleaner separation, better performance |

**Key insight:** Mode switching in a Pomodoro timer is simple enough that vanilla patterns are sufficient. Don't reach for state management libraries.

## Common Pitfalls

### Pitfall 1: Counting Sessions on Break Completion
**What goes wrong:** Session counter increments when both Pomodoro AND break complete, doubling the count
**Why it happens:** Using a generic "timer complete" handler without mode checking
**How to avoid:** Always check `if (timer.mode === 'pomodoro')` before incrementing sessions
**Warning signs:** Session count is 2x expected after completing a Pomodoro + break cycle

### Pitfall 2: Mode Switch Resets Running Timer Unexpectedly
**What goes wrong:** User clicks "Short Break" while Pomodoro is running at 15:00, timer stops
**Why it happens:** `switchMode()` doesn't preserve running state
**How to avoid:** Check if timer was running, stop, switch mode, restart if was running
**Warning signs:** Users complain that clicking mode buttons stops their timer

### Pitfall 3: Active Button State Not Updated
**What goes wrong:** Both mode buttons appear active, or neither does
**Why it happens:** Forgetting to remove `active` class from other buttons when switching
**How to avoid:** Remove `active` from ALL mode buttons first, then add to current
**Warning signs:** Visual inconsistency between selected mode and button appearance

### Pitfall 4: Session Display Not Prominent
**What goes wrong:** Users don't notice the session counter, think feature is missing
**Why it happens:** Counter placed in corner, small font, low contrast
**How to avoid:** Place counter prominently below timer display, use large readable text
**Warning signs:** Users ask "how do I see how many Pomodoros I've done?"

### Pitfall 5: Dynamic Property Lookup Fails
**What goes wrong:** `timer[mode]` returns `undefined` because mode string doesn't match property name
**Why it happens:** Mode string is "short-break" but property is `shortBreak`
**How to avoid:** Ensure mode strings exactly match timer object property names
**Warning signs:** Timer shows `NaN:NaN` or `undefined` after mode switch

## Code Examples

Verified patterns from official sources:

### Complete switchMode Implementation
```javascript
// Source: freshman.tech/pomodoro-timer + success criteria adaptation
function switchMode(mode) {
  const wasRunning = timer.interval !== null;

  // Stop current timer
  if (wasRunning) {
    clearInterval(timer.interval);
    timer.interval = null;
  }

  // Update mode state
  timer.mode = mode;

  // Set duration based on mode (dynamic property lookup)
  const durationMinutes = timer[mode]; // e.g., timer['pomodoro'] = 25
  timer.remainingTime = durationMinutes * 60;

  // Update mode button UI
  document.querySelectorAll('.mode-btn').forEach(btn => {
    btn.classList.toggle('active', btn.dataset.mode === mode);
  });

  // Update display
  updateDisplay();

  // Restart if was running (preserves running state per success criteria)
  if (wasRunning) {
    startTimer();
  }
}
```

### Session Counter with Display Update
```javascript
// Source: Pattern from dev.to/nooriameer + freshman.tech
function handleTimerComplete() {
  // Only increment sessions for completed Pomodoros
  if (timer.mode === 'pomodoro') {
    timer.sessions++;
    updateSessionDisplay();
  }

  // Play completion sound/notification (from Phase 2)
  playBeep();

  // Auto-switch to break after Pomodoro, back to Pomodoro after break
  const nextMode = timer.mode === 'pomodoro' ? 'shortBreak' : 'pomodoro';
  switchMode(nextMode);
}

function updateSessionDisplay() {
  const sessionDisplay = document.getElementById('session-count');
  sessionDisplay.textContent = `Pomodoros completed: ${timer.sessions}`;
}
```

### HTML Structure for Mode Buttons
```html
<!-- Source: Standard pattern from multiple tutorials -->
<div class="mode-buttons">
  <button class="mode-btn active" data-mode="pomodoro">Pomodoro</button>
  <button class="mode-btn" data-mode="shortBreak">Short Break</button>
</div>

<div id="timer-display">25:00</div>

<div id="session-count">Pomodoros completed: 0</div>

<div class="controls">
  <button id="start-btn">Start</button>
  <button id="stop-btn">Stop</button>
  <button id="reset-btn">Reset</button>
</div>
```

### CSS for Mode-Specific Theming
```css
/* Source: CSS custom properties pattern */
:root {
  --pomodoro-color: #e74c3c;
  --shortBreak-color: #27ae60;
  --text-color: #ecf0f1;
}

body {
  background-color: var(--pomodoro-color);
  transition: background-color 0.3s ease;
}

body[data-mode="shortBreak"] {
  background-color: var(--shortBreak-color);
}

.mode-btn {
  opacity: 0.6;
  transition: opacity 0.2s ease;
}

.mode-btn.active {
  opacity: 1;
  font-weight: bold;
}

#session-count {
  font-size: 1.2rem;
  margin-top: 1rem;
  color: var(--text-color);
}
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Separate click handlers per mode | Event delegation with data attributes | ~2020 | Less code, easier to extend |
| Multiple duration variables | Single state object with dynamic lookup | ~2018 | Cleaner state management |
| Manual CSS updates in JS | CSS custom properties | 2019-2020 | Better separation of concerns |

**Deprecated/outdated:**
- Using separate variables for each mode's duration (use object properties)
- Hard-coded button IDs in click handlers (use data attributes)
- Inline style manipulation for theming (use CSS custom properties)

## Open Questions

All questions for Phase 3 are resolved:

1. **Should breaks count toward session total?**
   - Answer: No. Only completed Pomodoros increment the counter. This is the standard Pomodoro technique.
   - Confidence: HIGH (official Pomodoro technique documentation)

2. **Should mode switch auto-start the timer?**
   - Answer: Per success criteria - "Mode switching preserves timer state (if running, keeps running with new duration)". Only auto-start if was running.
   - Confidence: HIGH (success criteria is explicit)

3. **Long break after 4 Pomodoros?**
   - Answer: Out of scope for Phase 3. REQUIREMENTS.md only includes TIMER-05 (5-minute break mode) and TIMER-06 (session counter). Long break is not a v1 requirement.
   - Confidence: HIGH (requirements are explicit)

## Sources

### Primary (HIGH confidence)
- [freshman.tech/pomodoro-timer](https://freshman.tech/pomodoro-timer/) - Complete switchMode pattern, session counting, mode button handling
- Project ARCHITECTURE.md - Mode transition patterns, state management approach
- Project PITFALLS.md - Timer-specific pitfalls already documented

### Secondary (MEDIUM confidence)
- [dev.to/nooriameer - Pomodoro Timer App](https://dev.to/nooriameer/how-to-create-a-pomodoro-timer-app-using-html-css-and-javascript-51i) - Session counter implementation pattern
- [Medium - Building a Modern Pomodoro Timer](https://medium.com/@francesco.saviano87/building-a-modern-pomodoro-timer-with-vanilla-javascript-a-complete-guide-48f7fd96cad9) - Mode switching patterns

### Tertiary (LOW confidence)
- [GeeksforGeeks - Pomodoro Timer](https://www.geeksforgeeks.org/javascript/create-a-pomodoro-timer-using-html-css-and-javascript/) - Basic timer patterns (no mode switching in their implementation)

## Metadata

**Confidence breakdown:**
- Standard patterns: HIGH - Multiple tutorials show identical patterns
- Architecture: HIGH - Extends established Phase 1-2 patterns
- Pitfalls: HIGH - Based on project PITFALLS.md + pattern analysis

**Research date:** 2026-01-26
**Valid until:** 60 days (patterns are stable, vanilla JS)
