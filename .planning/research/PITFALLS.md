# Domain Pitfalls: Live Demo + Pomodoro Timer

**Domain:** Live coding demonstration with Pomodoro timer implementation
**Researched:** 2026-01-26
**Context:** 20-minute live demo with copy-paste approach for mixed audience

## Critical Pitfalls

Mistakes that cause demo failure or audience disengagement.

### Pitfall 1: Demo Time Explosion (Scope Creep)
**What goes wrong:** "Quick features" during demo turn into 5-minute debugging sessions. Demo runs 30+ minutes instead of 20.

**Why it happens:**
- Audience asks "Can it do X?" and presenter adds features live
- Presenter sees "easy improvement" and implements it on the fly
- Technical issues consume time that was budgeted for features

**Consequences:**
- Lose audience attention (mixed audience zones out after 20 mins)
- Never reach the inspiring conclusion
- Demo feels chaotic, not professional
- Miss the "inspire people to try GSD" objective

**Prevention:**
- Scope lock: Define exactly 3-4 features before demo, no additions
- Time budgets per section: Planning (5 min), Core timer (8 min), Visual polish (5 min), Wrap-up (2 min)
- Pre-stage all code snippets: Every paste operation tested beforehand
- "Parking lot" slide: When asked about features, write them down for "future phases" but don't implement

**Detection Warning Signs:**
- 10 minutes in and timer isn't running yet
- Audience member suggests "it would be cool if..."
- You say "let me just quickly..." (nothing is quick in live demos)

**Demo Recovery:**
- Hard cut: "Great idea! Let's add that to phase 2. For now, let's see the timer work."
- Skip ahead: "I've pre-built the next part, let me show you..." (have backup code ready)
- Own it: "We're at 15 minutes and I want to make sure you see X, so I'm jumping to the finish"

---

### Pitfall 2: The Silent Failure (No User Feedback)
**What goes wrong:** Timer finishes, nothing happens. User doesn't know if it worked, browser crashed, or code failed.

**Why it happens:**
- Focused on timer logic, forgot notification/alert
- Browser blocks notification permission (not requested)
- Browser blocks audio autoplay (no user interaction)
- Background tab throttling stops timer but gives no indication

**Consequences:**
- User thinks the app is broken
- Pomodoro technique breaks (user doesn't know to take break)
- Demo feels incomplete ("where's the notification?")
- Lost trust in the GSD approach

**Prevention:**
- Multiple feedback channels: Visual alert + sound + browser notification
- Graceful degradation: If notification denied, show big modal instead
- Audio unlock pattern: Play silent audio on first user click to unlock autoplay
- Visual timer state: Show "Running", "Paused", "Complete" clearly
- Test in inactive tab: Verify timer still fires when tab not focused

**Detection Warning Signs:**
- Timer hits 0:00 but nothing changes on screen
- Console shows "Notification permission denied"
- Audio file loaded but `.play()` returns rejected promise
- Timer stops when you switch tabs

**Demo Recovery:**
- Show the recovery: "Notice how the browser blocked notifications? Here's how we handle that..."
- Turn it into teaching: "This is why we always need fallback UX"
- Use DevTools: Open console and show the error, then implement fix

---

### Pitfall 3: JavaScript Timer Drift
**What goes wrong:** Set timer for 25:00, but it actually takes 25:15 or 24:45 to complete. Timer loses accuracy over time.

**Why it happens:**
- Using `setInterval` which drifts due to event loop blocking
- Decrementing counter approach: `remainingSeconds--` compounds errors
- Background tab throttling: Browser slows timers to 1000ms when tab inactive
- CPU load, battery saving mode slow down `setTimeout` minimum delays

**Consequences:**
- User's "25 minute" Pomodoro is actually 26+ minutes
- Multiple Pomodoros compound the error (off by 10+ minutes after 3 sessions)
- Breaks trust in the tool
- Defeats purpose of time-boxing technique

**Prevention:**
- Date-based calculation: Store `endTime = Date.now() + duration`, calculate remaining on each tick
- Use `performance.now()` for high-precision timestamps
- Nested `setTimeout` over `setInterval`: Adjust delay based on actual elapsed time
- Self-correcting algorithm: Measure drift and adjust next interval
```javascript
// GOOD: Date-based
const endTime = Date.now() + (25 * 60 * 1000);
function tick() {
  const remaining = Math.max(0, endTime - Date.now());
  updateDisplay(remaining);
  if (remaining > 0) setTimeout(tick, 100);
}

// BAD: Counter-based
let remaining = 25 * 60;
setInterval(() => {
  remaining--; // Drifts over time
  updateDisplay(remaining);
}, 1000);
```

**Detection Warning Signs:**
- Timer completes at 25:03 or 24:57 instead of exactly 25:00
- Timer runs slower when tab in background
- Multiple runs show increasing variance

**Demo Recovery:**
- Show the problem: "Let me open DevTools and check actual time elapsed vs displayed time"
- Fix live: "This is why we use Date.now() instead of counting down"
- Turn it educational: "Common JavaScript pitfall - timers aren't guaranteed accurate"

---

### Pitfall 4: Copy-Paste Code Theater (Not Actually Live)
**What goes wrong:** Audience realizes it's all pre-written, feels deceived. Demo loses credibility.

**Why it happens:**
- Paste large blocks without explanation
- No typos, no mistakes (too perfect)
- Code works first try every time
- No visible decision-making or problem-solving

**Consequences:**
- Audience feels demo is fake or "rigged"
- Loses the "watch me use GSD" inspiration
- Technical audience calls it out
- Non-technical audience senses inauthenticity

**Prevention:**
- Narrate decisions: "I'm pasting this function, notice how it uses Date.now() because..."
- Show the source: Keep snippets visible in second window or split screen
- Make deliberate mistakes: Add a bug, then debug it (builds trust)
- Explain trade-offs: "I could use setInterval here, but that would drift, so instead..."
- Show GSD in action: "GSD gave me this implementation, let me walk through why it's good"

**Detection Warning Signs:**
- Audience looks skeptical during paste operations
- Someone asks "Did you write this beforehand?"
- No one is taking notes (they don't believe they could replicate it)

**Demo Recovery:**
- Acknowledge it: "Yes, I pre-wrote these snippets so we don't watch me type. But let me explain the logic..."
- Make it real: Intentionally introduce a bug, debug it live
- Show decision point: "Let me try a different approach..." and paste alternative implementation

---

### Pitfall 5: Technical Jargon Overload (Mixed Audience Alienation)
**What goes wrong:** Use terms like "event loop throttling," "Promise rejection," "setInterval drift" - non-technical audience tunes out.

**Why it happens:**
- Trying to sound expert to technical audience members
- Explaining code without translating to concepts
- Assuming everyone knows JavaScript/browser APIs
- Getting excited about technical solution and forgetting audience composition

**Consequences:**
- 60% of audience (non-technical) feels lost
- Lose potential GSD adopters who think "too technical for me"
- Demo becomes about JavaScript, not about GSD workflow
- Miss goal of "inspiring people to try GSD"

**Prevention:**
- Layered explanation: Start with concept, add technical detail for those who want it
- Analogies: "Timer drift is like a clock that runs slow - after a week it's 5 minutes behind"
- Visual aids: Show the problem visually, not just in code
- Pause for translation: "In simple terms, this means..."
- Focus on outcome: "This makes sure your timer is accurate" not "This uses Date.now() instead of decrementing"

**Detection Warning Signs:**
- Non-technical people scrolling on phones
- No questions from non-technical audience
- Technical people nodding, others looking confused
- Someone asks "Can you explain what that means?"

**Demo Recovery:**
- Self-correct: "Let me put that in simpler terms..."
- Ask audience: "Should I go deeper technically or focus on the concept?"
- Use the parking lot: "I'll share technical details in the project README for those interested"
- Refocus: "The key point is: GSD helps you avoid these pitfalls, here's how..."

---

### Pitfall 6: Demo Environment Sabotage
**What goes wrong:** Wi-Fi drops, battery dies, external display fails, microphone cuts out, browser crashes.

**Why it happens:**
- Didn't test A/V setup beforehand
- Running on low battery or unstable Wi-Fi
- Multiple apps consuming resources
- Browser extensions interfere
- Auto-updates kick in during demo

**Consequences:**
- Demo stops completely
- Scrambling to fix technical issues eats 5-10 minutes
- Lose audience attention and credibility
- Never recover momentum

**Prevention:**
- Full battery + power connected
- Close all apps except browser and terminal
- Dedicated demo browser profile: No extensions, clean cache
- Offline-capable: Host all assets locally, no external CDN dependencies
- Pre-connect displays: Test projector/screen share 30 min before
- Backup plan: Recording of the demo as fallback
- Kill notifications: DND mode, turn off Slack/email/messages
- Bandwidth test: If using cloud tools, verify connection speed

**Detection Warning Signs:**
- "Reconnecting..." appears on screen
- Battery warning pops up
- Fan starts spinning loud (CPU overload)
- Screen flickers or resolution changes

**Demo Recovery:**
- Failover to backup: "Let me show you a recording while I fix this..."
- Fill time productively: "While this loads, let me explain the roadmap we created..."
- Humor disarms: "And this is why we test twice - let me fix this quickly"
- Delegate: If presenting with someone, have them take over narration

---

## Moderate Pitfalls

Mistakes that cause delays or rough edges but are recoverable.

### Pitfall 7: No Visual Progress Indicator
**What goes wrong:** User stares at "25:00" for seconds before it changes to "24:59" - is it frozen?

**Prevention:**
- Show "running" state immediately (pulsing icon, progress ring)
- Update more frequently (every 100ms for smooth progress bar)
- Percentage complete in addition to time remaining

---

### Pitfall 8: Pause/Resume Complexity
**What goes wrong:** Pausing seems simple, but resume is broken - timer continues from wrong time.

**Prevention:**
- Store `remainingTime` on pause, not `elapsedTime`
- On resume, calculate new `endTime = Date.now() + remainingTime`
- Test: Pause at 20:00, wait 5 minutes, resume - should continue from 20:00 not 15:00

---

### Pitfall 9: Negative Timer Display
**What goes wrong:** Timer shows "00:-01", "00:-02" instead of stopping at zero.

**Prevention:**
- Always `Math.max(0, remaining)` when calculating display
- Clear interval/timeout when remaining <= 0
- Test: Let timer run 10 seconds past zero

---

### Pitfall 10: Browser Tab Throttling Invisibility
**What goes wrong:** Timer stops when user switches tabs, resumes when they return - loses track of time.

**Prevention:**
- Use date-based calculation (not counter) so timer auto-corrects on tab focus
- Page Visibility API: Detect when tab inactive, recalculate on focus
- Test: Start timer, switch tabs for 2 minutes, return - should show correct time

---

### Pitfall 11: Memory Leak from Uncleaned Intervals
**What goes wrong:** Create new interval on each start, never clear old ones - browser slows down.

**Prevention:**
```javascript
let timerId = null;
function startTimer() {
  if (timerId) clearInterval(timerId); // Clean old timer first
  timerId = setInterval(tick, 1000);
}
```
- Always store interval ID
- Clear on pause, stop, or restart
- Test: Start/stop 10 times, check browser memory in DevTools

---

### Pitfall 12: Audio File Not Preloaded
**What goes wrong:** Timer hits zero, 2-second delay while notification sound loads from network.

**Prevention:**
- Preload audio in HTML: `<audio id="done" preload="auto" src="done.mp3"></audio>`
- Or load on page ready: `const audio = new Audio('done.mp3'); audio.load();`
- Test on slow 3G throttled connection

---

### Pitfall 13: Notification Permission Too Late
**What goes wrong:** Ask for notification permission when timer completes - user misses it while denying permission.

**Prevention:**
- Request permission on first user interaction (start button click)
- Show permission state before starting timer
- Fallback modal if permission denied
- Never ask for permission without user gesture (browsers block it)

---

### Pitfall 14: "Just One More Feature" Creep
**What goes wrong:** Demo goes well, presenter thinks "I can add breaks tracking" and goes over time.

**Prevention:**
- Script the demo with hard time markers
- Set phone alarm for 18 minutes (2 min buffer for Q&A)
- Pre-decide what gets cut if running late
- Write on whiteboard: "Features for Phase 2" - point to it when tempted

---

## Minor Pitfalls

Mistakes that cause annoyance or polish issues but are easily fixed.

### Pitfall 15: Poor Contrast / Small Fonts
**What goes wrong:** Audience in back can't read the timer or code.

**Prevention:**
- Timer display: 48px+ font, high contrast colors
- Code editor: Zoom to 150-200%, remove minimap
- Test: Stand 10 feet from screen, can you read it?

---

### Pitfall 16: No Keyboard Shortcuts
**What goes wrong:** Clicking buttons during demo is slow and error-prone.

**Prevention:**
- Space = start/pause
- Escape = reset
- R = restart
- Announce shortcuts to audience: "I'll use space to start"

---

### Pitfall 17: Timer Doesn't Show When Complete
**What goes wrong:** Timer shows "00:00" but user doesn't know it's done vs still running.

**Prevention:**
- Change display on complete: "Done!" or "Time's up!" or show elapsed time "00:00 (+0:15)"
- Different color: Green for running, red for complete
- Animation: Pulse or bounce when complete

---

### Pitfall 18: No Way to Reset Mid-Session
**What goes wrong:** Start timer by accident, only option is wait 25 minutes or reload page.

**Prevention:**
- Reset button always visible
- Confirmation modal: "Are you sure? 12:30 remaining"
- Test: Start timer, immediately want to reset

---

### Pitfall 19: Hard-Coded Durations
**What goes wrong:** Timer locked to 25 minutes, can't demo shorter for time constraints.

**Prevention:**
- Make duration configurable (even if just in code constants)
- For demo, use 10-second timer to show complete cycle
- Show 25-minute timer in "real" version

---

### Pitfall 20: Inconsistent State on Refresh
**What goes wrong:** Reload page mid-timer, everything resets, user loses progress.

**Prevention:**
- localStorage: Persist timer state on every tick
- On page load: Check localStorage, resume if timer was running
- Show: "Resume timer? 14:23 remaining" modal on page load

---

## Demo-Specific Warnings

Phase-specific concerns for the live demo context.

### Phase: Pre-Demo (Setup)

**Likely Pitfall:** Untested projector setup causes 10-minute delay at start

**Mitigation:**
- Arrive 30 minutes early
- Test screen share or projector with exact laptop/browser
- Have HDMI + USB-C + VGA adapters
- Backup: Run demo on host's computer if yours won't connect

---

### Phase: Introduction (0-5 min)

**Likely Pitfall:** Spending too long on GSD explanation, no time left for coding

**Mitigation:**
- 2-slide max introduction: What is GSD (1 slide), What we're building (1 slide)
- Set expectations: "20 minutes, building a Pomodoro timer"
- Defer questions: "Hold questions until the end so we have time to build"

---

### Phase: Core Implementation (5-15 min)

**Likely Pitfall:** Debugging session eats all time

**Mitigation:**
- Test every paste operation beforehand
- Pre-stage working code in backup file
- If stuck >2 minutes, say "I have working version, let me show that instead"
- Make intentional bugs obvious and quick to fix

---

### Phase: Visual Polish (15-18 min)

**Likely Pitfall:** Perfectionism - tweaking colors and layout forever

**Mitigation:**
- One pass only: "Let's add some quick styling"
- Use utility CSS (Tailwind) for speed
- If running late, skip styling: "Looks aren't critical for a demo, it works!"

---

### Phase: Wrap-up (18-20 min)

**Likely Pitfall:** No time left for call-to-action

**Mitigation:**
- Hard stop at 18 minutes: Move to conclusion slide even if features incomplete
- Conclusion prepared: "Here's how to try GSD yourself" (1 min)
- Questions deferred: "I'll stick around after for questions"

---

## Recovery Strategies Reference Card

Print this and keep it visible during demo:

| Scenario | Recovery Action |
|----------|----------------|
| Running 5+ min over | Skip to pre-built final version, narrate what you skipped |
| Code doesn't work | "I have working version here..." (load backup file) |
| Audience asks for feature | "Great for Phase 2!" Write it down, don't implement |
| Technical jargon confusion | "In simple terms: [analogy]" |
| Wi-Fi drops | Switch to local files or show recording |
| Audience distracted | Ask a question: "Who uses Pomodoro now?" Re-engage |
| Notification blocked | "This shows why fallbacks matter, watch this..." (implement modal) |
| Timer drifts | Make it educational: "Common bug, here's the fix..." |

---

## Pre-Demo Checklist

Print and complete 1 hour before:

**Environment**
- [ ] Full battery + charger connected
- [ ] All apps closed except browser and terminal
- [ ] Browser notifications/extensions disabled
- [ ] DND mode enabled (no pop-ups)
- [ ] Wi-Fi connected and tested
- [ ] Display/projector tested with this laptop

**Code Preparation**
- [ ] All snippets tested in order
- [ ] Backup file with working version ready
- [ ] Short demo timer (10 sec) configured for quick cycles
- [ ] Full timer (25 min) ready to show
- [ ] Audio file loads and plays
- [ ] Notification permission flow tested

**Presentation**
- [ ] Slides loaded (2 slides max)
- [ ] Timer visible on screen (phone or watch)
- [ ] Recovery reference card printed
- [ ] Parking lot whiteboard/slide ready
- [ ] Call-to-action slide prepared

**Backup Plans**
- [ ] Recorded demo video available
- [ ] Can run on host computer if needed
- [ ] Offline copies of all assets
- [ ] Pre-built final version in separate file

---

## Sources

Live Coding Demo Research:
- [Surviving live code interviews for newbies - DEV Community](https://dev.to/heyjtk/surviving-live-code-interviews-for-newbies-5hn6)
- [Live Coding — LaunchCode Career Readiness](https://education.launchcode.org/career-readiness/interviews/live-coding.html)
- [How to Master Live-Coding Challenges - Medium](https://medium.com/@shraddharao_/how-to-master-live-coding-challenges-the-hard-way-ba767b84160c)
- [8 AI Code Generation Mistakes Devs Must Fix To Win 2026 - Futurism](https://vocal.media/futurism/8-ai-code-generation-mistakes-devs-must-fix-to-win-2026)

Demo Failures and Prevention:
- [The Top 6 Live Product Demo Fails Of All Time - Walnut](https://www.walnut.io/blog/product-demos/top-5-product-demo-fails/)
- [Meta DDoS'd itself during smart glasses AI demo failures](https://www.techbuzz.ai/articles/meta-ddos-d-itself-during-smart-glasses-ai-demo-failures)
- [Common Mistakes to Avoid During Technical Demos - Mayuresh Shilotri](https://shilotri.com/sales/sales-engineering/common-mistakes-to-avoid-during-technical-demos/)
- [How to give a perfect demo - Scott Berkun](https://scottberkun.com/2011/how-to-give-a-perfect-demo/)

Technical Presentation Best Practices:
- [Slides Guideline For A Technical Presentation](https://www.cs.ucr.edu/~michalis/TECHWRITING/presentation-20.html)
- [How to give a technical presentation - University of Washington](https://homes.cs.washington.edu/~mernst/advice/giving-talk.html)
- [8 Essential Elements of a 20 Minute Presentation](https://endurancelearning.com/blog/the-20-minute-presentation-checklist/)
- [Presentation Design Trends 2026 - Sketchbubble](https://www.sketchbubble.com/blog/presentation-design-trends-2026-the-ultimate-guide-to-future-ready-slides/)
- [Presentation Skills Goals for 2026 - Winning Presentations](https://winningpresentations.com/presentation-skills-goals-2026/)

Pomodoro Timer Implementation:
- [Top 11 Pomodoro Timer Apps for 2026 - Reclaim](https://reclaim.ai/blog/best-pomodoro-timer-apps)
- [Pomodoro Technique - Wikipedia](https://en.wikipedia.org/wiki/Pomodoro_Technique)
- [Official Pomodoro Technique](https://www.pomodorotechnique.com/)
- [FocusPomo Pomodoro Timer Reviews](https://justuseapp.com/en/app/1528322796/focuspomo-pomodoro-study-timer/reviews)

JavaScript Timer Accuracy:
- [Why Javascript timer is unreliable - Medium](https://abhi9bakshi.medium.com/why-javascript-timer-is-unreliable-and-how-can-you-fix-it-9ff5e6d34ee0)
- [Scheduling: setTimeout and setInterval](https://javascript.info/settimeout-setinterval)
- [Creating Accurate Timers in JavaScript - SitePoint](https://www.sitepoint.com/creating-accurate-timers-in-javascript/)
- [setTimeout and setInterval Uses and Limitations - Syncfusion](https://www.syncfusion.com/blogs/post/settimeout-setinterval-uses-limitations)
- [Implementing Countdown Timers with Algorithms - AlgoCademy](https://algocademy.com/blog/implementing-countdown-timers-with-algorithms-a-comprehensive-guide/)

Browser API Restrictions:
- [Browser Notification Permission - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Notification/requestPermission_static)
- [Autoplay policy in Chrome - Chrome Developers](https://developer.chrome.com/blog/autoplay)
- [Bypassing Browser Autoplay Restrictions - Medium](https://medium.com/@harryespant/bypassing-browser-autoplay-restrictions-a-smart-approach-to-notification-sounds-9e14ca34e5c5)
- [Autoplay guide for media and Web Audio APIs - MDN](https://developer.mozilla.org/en-US/docs/Web/Media/Autoplay_guide)

Mixed Audience Communication:
- [Technical vs Non-Technical Audiences - Technical Leaders](https://www.technical-leaders.com/post/technical-vs-non-technical-audiences-communication-strategies)
- [What are the best practices for delivering a technical demo to a non-technical audience? - LinkedIn](https://www.linkedin.com/advice/3/what-best-practices-delivering-technical-demo)
- [How to Present to Non-Technical Audiences - Mel Sherwood](https://www.melsherwood.com/blogposts/2025/2/9/how-to-present-to-non-technical-audiences)
- [How to Explain Technical Concepts to Non-Technical Audiences - Leapica](https://leapica.com/explain-technical-concepts-non-technical-audiences/)

Scope Creep and Time Management:
- [What Is Scope Creep - Projectworks](https://www.projectworks.com/blog/scope-creep)
- [Best Practices to Avoid Scope Creep - Workamajig](https://www.workamajig.com/blog/project-scope-guide/scope-creep)
- [What is Scope Creep and how to avoid it - TeamGantt](https://www.teamgantt.com/project-management-guide/taming-scope-creep)

Demo Recovery:
- [Overcoming Common Demo Challenges - Powerhouse Sales Engineering](https://www.powerhousesalesengineering.com/demos/overcoming-demo-challenges/)
- [How to Prepare a Software Demo Presentation for 2025 - Reprise](https://www.reprise.com/resources/blog/software-demo-presentation)
