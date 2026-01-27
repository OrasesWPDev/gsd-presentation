# Phase 1: Timer Foundation - Research

**Researched:** 2026-01-26
**Domain:** Static HTML/CSS/JS timer structure, state management, MM:SS display formatting
**Confidence:** HIGH

## Summary

Phase 1 establishes the visual and structural foundation for the Pomodoro timer. This phase delivers a static UI that displays "25:00" in a centered, readable format, with mode buttons (Pomodoro, Short Break) and a start/stop button visible but non-functional. The phase also creates the state management object and a working `updateDisplay()` function.

The standard approach is a **single-file HTML architecture** with inline CSS and JavaScript. This eliminates build complexity, allows the entire codebase to be visible at once, and provides instant feedback (double-click HTML file to run). State is managed via a plain JavaScript object, and display formatting uses the manual calculation pattern with `padStart()` for zero-padding.

**Primary recommendation:** Use a single `index.html` file with inline `<style>` and `<script>` sections. State is a plain object, display updates via pure function, no frameworks or build tools.

## Standard Stack

The established libraries/tools for this domain:

### Core
| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| HTML5 | Living Standard | Page structure | Zero build step, semantic elements for accessibility |
| CSS3 | Living Standard | Styling and layout | Custom properties for easy theming, flexbox for centering |
| Vanilla JavaScript | ES2025 | State and DOM manipulation | No dependencies, instant feedback, universal comprehension |

### Supporting
| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| None | - | - | This phase requires no external dependencies |

### Alternatives Considered
| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| Vanilla JS | React/Vue/Svelte | Build tools required, adds setup time, overkill for 50 lines |
| Inline CSS | Tailwind | Requires npm or CDN, clutters HTML for live coding |
| Single file | Multi-file | Requires file switching during demo, no benefit at this scale |

**Installation:**
```bash
# No installation needed. Create single file:
mkdir -p pomodoro-timer
touch pomodoro-timer/index.html
```

## Architecture Patterns

### Recommended Project Structure
```
pomodoro-timer/
└── index.html        # Everything: HTML structure, CSS styles, JS logic
```

### Pattern 1: Single-File Self-Contained HTML
**What:** All HTML, CSS, and JavaScript in one file with clear section comments
**When to use:** Demos, small apps, teaching contexts, prototypes
**Example:**
```html
<!-- Source: MDN Best Practices + Existing project ARCHITECTURE.md -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pomodoro Timer</title>
    <style>
        /* ============================================
           CSS STYLES
           ============================================ */
        :root {
            --primary-color: #e74c3c;
            --bg-color: #2c3e50;
            --text-color: #ecf0f1;
        }
        /* Layout, typography, components... */
    </style>
</head>
<body>
    <!-- HTML Structure -->

    <script>
        // ============================================
        // STATE MANAGEMENT
        // ============================================

        // ============================================
        // DISPLAY UPDATES
        // ============================================

        // ============================================
        // EVENT LISTENERS
        // ============================================
    </script>
</body>
</html>
```

### Pattern 2: Plain Object State Management
**What:** All timer configuration and runtime state in a single plain object
**When to use:** Simple apps where React/Redux complexity isn't warranted
**Example:**
```javascript
// Source: Existing project ARCHITECTURE.md
const timer = {
    // Configuration (static values)
    durations: {
        pomodoro: 25,    // minutes
        shortBreak: 5
    },

    // Runtime state (changes during execution)
    currentMode: 'pomodoro',
    remainingSeconds: 25 * 60,
    isRunning: false,
    intervalId: null
};
```

### Pattern 3: Pure Function Display Update
**What:** A function that takes state and updates the DOM, with no side effects
**When to use:** Any time display needs to reflect state changes
**Example:**
```javascript
// Source: MDN + Timer implementation best practices
function updateDisplay() {
    const minutes = Math.floor(timer.remainingSeconds / 60);
    const seconds = timer.remainingSeconds % 60;
    const formatted = `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
    document.getElementById('timer-display').textContent = formatted;
}
```

### Anti-Patterns to Avoid
- **Class-based timer:** Requires explaining `this` context, overkill for demo
- **Counter stored in DOM:** State in data attributes leads to string conversion bugs
- **Multiple state variables:** Scattered `let` declarations harder to debug than single object
- **Inline event handlers:** `onclick="start()"` mixes HTML and JS, not the modern pattern

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Zero-padding | String concatenation with conditionals | `String.padStart(2, '0')` | Native, handles all cases, cleaner |
| Centering layout | Manual margin/position calculations | CSS flexbox | `display: flex; justify-content: center; align-items: center` is bulletproof |
| Color variables | Hardcoded hex values everywhere | CSS custom properties | `:root { --primary: #e74c3c }` enables easy theming |
| Time formatting | Division with parseInt and string concatenation | `Math.floor()` + `%` + `padStart()` | Standard pattern, readable, handles edge cases |

**Key insight:** This phase uses only native browser APIs. The complexity is in getting patterns right, not in library selection.

## Common Pitfalls

### Pitfall 1: Forgetting Zero-Padding
**What goes wrong:** Timer shows "5:3" instead of "05:03" when minutes or seconds < 10
**Why it happens:** Basic string concatenation: `minutes + ":" + seconds`
**How to avoid:** Always use `padStart(2, '0')` on both minutes and seconds strings
**Warning signs:** Timer looks fine at "25:00" but breaks at "09:05"

### Pitfall 2: State Object Missing Critical Fields
**What goes wrong:** Later phases fail because expected state fields don't exist
**Why it happens:** Phase 1 creates minimal state, doesn't anticipate Phase 2 needs
**How to avoid:** Include placeholder fields that Phase 2 will use:
```javascript
const timer = {
    durations: { pomodoro: 25, shortBreak: 5 },
    currentMode: 'pomodoro',
    remainingSeconds: 25 * 60,
    isRunning: false,      // Phase 2 will toggle this
    intervalId: null       // Phase 2 will store timer ID here
};
```
**Warning signs:** Phase 2 planner asks "where do I store the interval ID?"

### Pitfall 3: DOM Selection Inside Functions
**What goes wrong:** Performance degradation from repeated `querySelector` calls
**Why it happens:** Selecting elements inside update functions that run every second
**How to avoid:** Cache DOM references at module scope:
```javascript
// GOOD: Cache once
const displayElement = document.getElementById('timer-display');
function updateDisplay() {
    displayElement.textContent = formatTime(timer.remainingSeconds);
}

// BAD: Select every time
function updateDisplay() {
    document.getElementById('timer-display').textContent = formatTime(timer.remainingSeconds);
}
```
**Warning signs:** For Phase 1 this is minor; becomes critical in Phase 2 with 1-second updates

### Pitfall 4: Hardcoded Duration in Multiple Places
**What goes wrong:** Timer shows "25:00" but state says 30 minutes, inconsistent behavior
**Why it happens:** Initial display is hardcoded HTML, state object has different value
**How to avoid:** Call `updateDisplay()` on page load to sync display with state:
```javascript
// At end of script
updateDisplay();  // Display reflects state.durations.pomodoro
```
**Warning signs:** Changing `pomodoro: 25` to `pomodoro: 30` doesn't change what user sees

### Pitfall 5: Missing Viewport Meta Tag
**What goes wrong:** Timer appears tiny on mobile devices
**Why it happens:** Omitting `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
**How to avoid:** Include viewport meta in `<head>` (even if demo is desktop-only)
**Warning signs:** Timer looks fine on laptop, unreadable on phone during demo

## Code Examples

Verified patterns from official sources:

### MM:SS Time Formatting (TIMER-03)
```javascript
// Source: MDN + GeeksforGeeks time formatting patterns
// https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padStart

function formatTime(totalSeconds) {
    const minutes = Math.floor(totalSeconds / 60);
    const seconds = totalSeconds % 60;
    return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
}

// Usage:
formatTime(1500);  // "25:00"
formatTime(125);   // "02:05"
formatTime(9);     // "00:09"
formatTime(0);     // "00:00"
```

### Complete State Object
```javascript
// Source: Existing project ARCHITECTURE.md
const timer = {
    // Duration configuration (minutes)
    durations: {
        pomodoro: 25,
        shortBreak: 5
    },

    // Current state
    currentMode: 'pomodoro',    // 'pomodoro' | 'shortBreak'
    remainingSeconds: 25 * 60,  // Derived from durations[currentMode] * 60

    // Phase 2 will use these
    isRunning: false,
    intervalId: null
};
```

### Display Update Function
```javascript
// Source: MDN DOM manipulation + project patterns
const displayElement = document.getElementById('timer-display');

function updateDisplay() {
    displayElement.textContent = formatTime(timer.remainingSeconds);
}

// Call on page load to sync
updateDisplay();
```

### HTML Structure for Timer UI
```html
<!-- Source: Project ARCHITECTURE.md + semantic HTML best practices -->
<div class="timer-container">
    <div class="mode-buttons">
        <button id="btn-pomodoro" class="mode-btn active">Pomodoro</button>
        <button id="btn-short-break" class="mode-btn">Short Break</button>
    </div>

    <div id="timer-display" class="timer-display">25:00</div>

    <div class="control-buttons">
        <button id="btn-start-stop" class="control-btn">Start</button>
    </div>
</div>
```

### CSS Flexbox Centering
```css
/* Source: MDN Flexbox Guide */
body {
    min-height: 100vh;
    margin: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: var(--bg-color);
}

.timer-display {
    font-size: 6rem;
    font-family: 'Courier New', monospace;
    color: var(--text-color);
}
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| jQuery DOM selection | Native `querySelector` | ~2016 | No dependencies needed |
| Manual string formatting | Template literals | ES6 (2015) | Cleaner: `` `${min}:${sec}` `` |
| Manual zero-padding | `String.padStart()` | ES2017 | No conditional logic needed |
| Separate CSS files for small projects | Inline `<style>` in single file | Always valid | Faster for demos, no file management |

**Deprecated/outdated:**
- `document.getElementById` is still valid but `querySelector` is more flexible
- `var` keyword: Use `const`/`let` instead
- String concatenation for formatting: Use template literals

## Open Questions

Things that couldn't be fully resolved:

1. **Font choice for timer display**
   - What we know: Monospace fonts prevent layout shift when digits change
   - What's unclear: Should use system font or web font?
   - Recommendation: Use `'Courier New', monospace` for zero dependencies; defer web font discussion to polish phase

2. **Accessibility for screen readers**
   - What we know: `aria-live="polite"` can announce time changes
   - What's unclear: How often to announce (every second is noisy)?
   - Recommendation: Add `aria-live` region but set to `off` initially; enable as enhancement later

## Sources

### Primary (HIGH confidence)
- [MDN: setInterval](https://developer.mozilla.org/en-US/docs/Web/API/Window/setInterval) - Timer mechanics, return value, clearInterval pattern
- [MDN: Date.now()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/now) - Timestamp precision, browser support
- [MDN: String.padStart()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/padStart) - Zero-padding pattern
- Existing project research: `.planning/research/STACK.md`, `.planning/research/ARCHITECTURE.md`

### Secondary (MEDIUM confidence)
- [GeeksforGeeks: Convert seconds to time string](https://www.geeksforgeeks.org/javascript/how-to-convert-seconds-to-time-string-format-hhmmss-using-javascript/) - MM:SS formatting patterns
- [Bobby Hadz: Convert seconds to HH:MM:SS](https://bobbyhadz.com/blog/javascript-convert-seconds-to-hh-mm-ss) - Additional formatting examples

### Tertiary (LOW confidence)
- None - All patterns verified with primary sources

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - Vanilla HTML/CSS/JS is established best practice for demos
- Architecture: HIGH - Single-file pattern verified in project research
- Display formatting: HIGH - MDN-verified `padStart()` pattern
- Pitfalls: HIGH - Based on project PITFALLS.md + MDN documentation

**Research date:** 2026-01-26
**Valid until:** 2026-03-26 (stable patterns, 60-day validity)
