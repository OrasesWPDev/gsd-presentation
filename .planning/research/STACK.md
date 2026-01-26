# Technology Stack

**Project:** GSD Training Demo - Pomodoro Timer
**Researched:** 2026-01-26
**Overall Confidence:** HIGH

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why | Confidence |
|------------|---------|---------|-----|------------|
| HTML5 | Latest (Living Standard) | Structure and semantics | Zero build step, universal browser support, semantic timer display | HIGH |
| CSS3 with Custom Properties | Latest (Living Standard) | Styling and theming | Modern, no preprocessor needed, live demo-friendly with instant visual feedback | HIGH |
| Vanilla JavaScript (ES6+) | ES2025 | Application logic | Zero dependencies, no build tools, instant feedback loop for live coding | HIGH |

### Web APIs Used

| API | Purpose | Why | Confidence |
|-----|---------|-----|------------|
| `setTimeout` / `setInterval` | Timer countdown mechanism | Standard, reliable, universally supported since 2015 | HIGH |
| Web Audio API (`AudioContext`) | Audio notification beep | Native tone generation, no audio files needed, simple oscillator pattern | HIGH |
| Notifications API (optional) | Desktop notifications when timer completes | Good for demo but requires HTTPS and user permission - make it optional | MEDIUM |
| CSS Custom Properties (variables) | Dynamic theming | Modern, no preprocessor, can demonstrate live CSS updates | HIGH |

### File Structure

```
pomodoro-timer/
├── index.html          # Single file with inline CSS and JS
└── README.md           # Setup and demo instructions (optional)
```

**Rationale:** Single HTML file maximizes demo speed. Everything inline means:
- Open file in browser = instant result
- No server needed (file:// protocol works)
- Edit-refresh cycle is immediate
- Audience sees all code in one place

## Why Vanilla Stack for This Demo

### 1. Zero Setup Time
**Critical for 20-minute demo:** No npm install, no build process, no dev server. Double-click HTML file and it runs.

### 2. Instant Feedback Loop
- Edit JavaScript → Save → Refresh = 2 seconds
- CSS changes visible immediately
- No transpilation, bundling, or hot-module-replacement complexity

### 3. Universal Comprehension
**Mixed audience (devs, BAs, PMs):** Everyone can read HTML/CSS/JS. No framework-specific syntax to explain.

### 4. Modern JavaScript is Powerful Enough
In 2025, vanilla JavaScript includes:
- `const`/`let` block scoping
- Arrow functions for clean syntax
- Template literals for string interpolation
- `querySelector` / `querySelectorAll` for DOM manipulation
- `classList` API for CSS class management
- Native `fetch` for HTTP (not needed here)

**No framework needed** for a timer with start/stop/reset buttons.

### 5. Demonstrates Core Web Platform
Shows that the browser platform itself is capable. Relevant for audience understanding "what's actually possible without tools."

## Alternatives Considered and Why NOT

| Alternative | Why NOT for This Demo | When You'd Use It |
|-------------|----------------------|-------------------|
| React / Vue / Svelte | Requires build tools (Vite/webpack), npm dependencies, JSX/template syntax to explain. Setup time kills demo momentum. | Production apps with complex state management |
| TypeScript | Requires transpilation. Adds type syntax to explain. Not worth it for 50 lines of code. | Large codebases, team projects with type safety needs |
| Tailwind CSS | Requires npm, PostCSS, or CDN. Classes clutter HTML during live coding. | Design system applications, rapid prototyping |
| jQuery | Outdated in 2025. Modern vanilla JS has same DX. Adds unnecessary dependency. | Legacy codebases (maintenance only) |
| CSS Preprocessors (Sass/Less) | Requires compilation. Custom properties (CSS variables) cover theming needs. | Complex design systems with deep nesting |
| Audio Files (MP3/WAV) | Requires hosting files, audio file selection overhead. Web Audio API generates tones in 5 lines. | Apps with rich soundscapes, music players |
| Service Workers | Overkill for local-only demo. Adds complexity for background sync/offline that isn't needed. | PWAs, offline-first apps |
| Build Tools (Vite/webpack) | Setup time, configuration, node_modules bloat. Antithetical to "simple demo." | Multi-file projects, production optimization |

## Installation

**None required.** This is the point.

For demo presenter:
```bash
# Create project
mkdir pomodoro-timer
cd pomodoro-timer
touch index.html

# Open in browser
open index.html  # macOS
start index.html # Windows
xdg-open index.html # Linux
```

For demo participants following along:
1. Create `index.html` file
2. Open in any browser
3. Start coding

## Code Structure Recommendations

### Single File Pattern

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pomodoro Timer</title>
    <style>
        /* CSS here - use :root for custom properties */
        :root {
            --primary-color: #e74c3c;
            --bg-color: #2c3e50;
            --text-color: #ecf0f1;
        }
        /* Component styles... */
    </style>
</head>
<body>
    <!-- HTML structure here -->

    <script>
        // JavaScript here
        // Use clear, readable patterns for live coding

        // State
        let timeLeft = 25 * 60; // 25 minutes in seconds
        let isRunning = false;

        // DOM references
        const display = document.querySelector('.timer-display');
        const startBtn = document.querySelector('.start-btn');

        // Functions
        function startTimer() { /* ... */ }
        function stopTimer() { /* ... */ }
        function resetTimer() { /* ... */ }
        function updateDisplay() { /* ... */ }
        function playBeep() { /* ... */ }

        // Event listeners
        startBtn.addEventListener('click', startTimer);
    </script>
</body>
</html>
```

### Why This Structure Works for Demo

1. **Top-to-bottom readability**: HTML → CSS → JS matches mental model
2. **No file switching**: Everything visible in one editor window
3. **Clear sections**: Comments delineate structure without complex imports
4. **Progressive enhancement**: Can build feature-by-feature during demo

## Demo-Friendly JavaScript Patterns

### Use Descriptive Variables
```javascript
// Good for demo
let timeRemainingInSeconds = 25 * 60;
let timerIsActive = false;

// Avoid in demo
let t = 1500;
let a = false;
```

### Prefer Function Declarations Over Expressions
```javascript
// Good - hoisted, easier to explain
function startTimer() { /* ... */ }

// Less demo-friendly - needs const/let explanation
const startTimer = () => { /* ... */ };
```

### Use setInterval with Clear Cleanup
```javascript
let intervalId = null;

function startTimer() {
    if (intervalId !== null) return; // Prevent multiple intervals

    intervalId = setInterval(() => {
        timeLeft--;
        updateDisplay();

        if (timeLeft <= 0) {
            stopTimer();
            playBeep();
        }
    }, 1000);
}

function stopTimer() {
    if (intervalId !== null) {
        clearInterval(intervalId);
        intervalId = null;
    }
}
```

### Web Audio Pattern for Beep
```javascript
function playBeep() {
    const audioContext = new (window.AudioContext || window.webkitAudioContext)();
    const oscillator = audioContext.createOscillator();
    const gainNode = audioContext.createGain();

    oscillator.connect(gainNode);
    gainNode.connect(audioContext.destination);

    oscillator.frequency.value = 440; // A4 note
    oscillator.type = 'sine';
    gainNode.gain.value = 0.3; // 30% volume

    oscillator.start(audioContext.currentTime);
    oscillator.stop(audioContext.currentTime + 0.2); // 200ms beep
}
```

## Browser Compatibility

All recommended technologies have universal support (95%+ global usage):

| Technology | Browser Support | Notes |
|------------|----------------|-------|
| HTML5 | Universal | Living Standard since 2019 |
| CSS Custom Properties | Chrome 49+, Firefox 31+, Safari 9.1+, Edge 15+ | All modern browsers since 2016 |
| ES6 JavaScript | Chrome 51+, Firefox 54+, Safari 10+, Edge 15+ | const/let/arrow functions widely supported since 2016 |
| `setTimeout`/`setInterval` | Universal | Available since JavaScript 1.0 |
| Web Audio API | Chrome 35+, Firefox 25+, Safari 14.1+, Edge 79+ | Widely available since 2021 |
| Notifications API (optional) | Desktop: 95%+ support, Mobile: partial | HTTPS required, optional feature |

**Target:** Any browser released in the last 5 years will work perfectly.

## Known Limitations and Workarounds

### 1. Background Tab Throttling
**Issue:** Browsers throttle timers in inactive tabs (Firefox: 1s minimum, Chrome varies).

**Impact:** Timer may drift if user switches tabs.

**Mitigation for Demo:**
- Mention this is expected browser behavior
- Alternative: Use `Date.now()` timestamps instead of decrementing counter
- Not critical for 20-minute demo

### 2. Notifications Require HTTPS
**Issue:** `Notification.requestPermission()` requires secure context.

**Impact:** Won't work with `file://` protocol.

**Mitigation for Demo:**
- Make notifications optional/"bonus feature"
- Show Web Audio beep as primary notification
- If needed, use local server: `python -m http.server 8000`

### 3. Autoplay Audio Restrictions
**Issue:** Browsers block audio without user interaction.

**Impact:** Beep won't play if timer auto-starts on page load.

**Mitigation for Demo:**
- Audio in response to button click works fine
- Only plays after timer completes (user-initiated chain)
- Not an issue for typical usage

### 4. No Module System
**Issue:** Single file means no ES6 imports.

**Impact:** Can't split code into modules.

**Mitigation for Demo:**
- Not needed for <200 lines of code
- Use clear comments to section code
- If demo grows: Progressive enhancement story ("when you need modules, add build tools")

## Performance Considerations

For a minimal Pomodoro timer, performance is non-issue:

- **DOM operations:** <10 elements, minimal reflows
- **Timer updates:** Once per second, trivial computational load
- **Memory:** Negligible (few variables, no large data structures)
- **Load time:** <1KB HTML, instant parse and render

**Optimization not needed.** Focus demo on clarity, not performance.

## Security Considerations

### Content Security Policy
If deployed with strict CSP:
```html
<meta http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'unsafe-inline'; style-src 'unsafe-inline';">
```

**For demo:** CSP not needed, running locally.

### XSS Prevention
- No user input stored or rendered (timer values are hardcoded or numeric)
- No innerHTML with user data
- No eval() or Function() constructor

**For demo:** Not applicable, no user input.

## Accessibility Considerations

Should be covered in implementation, not stack choice:

- Semantic HTML (`<button>`, `<time>`, proper headings)
- ARIA labels where needed (`aria-live` for timer updates)
- Keyboard navigation (buttons focusable, Enter/Space work)
- Color contrast (WCAG AA minimum)
- No audio-only notifications (visual + audio redundancy)

**Stack supports all of this natively.**

## Testing Strategy

For a 20-minute demo, formal testing is out of scope. Manual verification:

1. Start timer → countdown begins
2. Pause timer → countdown stops
3. Resume timer → countdown continues from paused value
4. Reset timer → returns to 25:00
5. Timer reaches 0:00 → beep plays
6. Refresh page → state doesn't persist (expected, no localStorage)

**No testing framework needed.** Browser DevTools sufficient.

## Deployment

Not needed for training demo. If deploying for reference:

### Option 1: GitHub Pages (Static Hosting)
```bash
git init
git add index.html
git commit -m "Initial commit"
git remote add origin https://github.com/username/pomodoro-demo.git
git push -u origin main

# Enable GitHub Pages in repo settings
# URL: https://username.github.io/pomodoro-demo/
```

### Option 2: Netlify Drop
Drag and drop `index.html` to [Netlify Drop](https://app.netlify.com/drop).

### Option 3: Local Server (for HTTPS notifications)
```bash
python -m http.server 8000
# Open http://localhost:8000
```

## Stack Evolution Path

This stack is intentionally minimal for demo. Natural progression for production:

1. **Add localStorage** → Persist timer state across refreshes
2. **Split into files** → `index.html`, `style.css`, `app.js`
3. **Add TypeScript** → Type safety for growing codebase
4. **Introduce build tools** → Vite for dev server, bundling
5. **Add framework** → React/Vue if state complexity grows
6. **Progressive Web App** → Service worker for offline, installability

**Key point for demo:** Start simple, add complexity only when needed.

## Sources

### High Confidence (Official Documentation)
- [MDN: Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
- [MDN: setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/Window/setTimeout)
- [MDN: Notifications API](https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API/Using_the_Notifications_API)
- [MDN: CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Guides/Cascading_variables/Using_custom_properties)
- [Can I Use: Notification API](https://caniuse.com/mdn-api_notification)

### Medium Confidence (Community Best Practices, Verified)
- [Vanilla JavaScript Is Quietly Taking Over Again](https://medium.com/@arkhan.khansb/vanilla-javascript-is-quietly-taking-over-again-heres-why-developers-are-switching-back-5ee1588e2bfa)
- [React vs. Vanilla JavaScript: What to Choose in 2025?](https://dev.to/purushoth_26/react-vs-vanilla-javascript-what-to-choose-in-2025-5ejb)
- [Why Vanilla JavaScript Is Making a Comeback in 2025](https://devtechinsights.com/vanilla-javascript-comeback-2025/)
- [Modern JavaScript Best Practices You Should Follow in 2025](https://ashishmisal.medium.com/modern-javascript-best-practices-you-should-follow-in-2025-74a5a73887db)

### Low Confidence (Tutorial References, Unverified)
- [How to build a Pomodoro Timer App with JavaScript](https://freshman.tech/pomodoro-timer/)
- [Create a Pomodoro Timer using HTML CSS and JavaScript - GeeksforGeeks](https://www.geeksforgeeks.org/javascript/create-a-pomodoro-timer-using-html-css-and-javascript/)
- [How to easily generate a Beep sound with JavaScript](https://ourcodeworld.com/articles/read/1627/how-to-easily-generate-a-beep-notification-sound-with-javascript)

## Summary

**Recommended stack:** Pure HTML5 + CSS3 + Vanilla JavaScript in a single file.

**Rationale:** Optimized for live demo context - zero setup, instant feedback, universal comprehension, demonstrates core web platform capabilities.

**Confidence level:** HIGH - All technologies are standard, mature, and universally supported. No experimental features, no edge cases.

**Critical for success:** Resist temptation to add frameworks or build tools. Simplicity IS the feature for a 20-minute demo.
