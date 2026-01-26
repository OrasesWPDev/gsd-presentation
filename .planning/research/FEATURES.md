# Feature Landscape: Minimal Pomodoro Timer for Live Demo

**Domain:** Productivity timer (Pomodoro Technique)
**Context:** 20-minute live demo of GSD workflow
**Audience:** Developers, BAs, PMs (mixed technical)
**Goal:** Inspire people to try GSD, not build the perfect timer
**Researched:** 2026-01-26

## Executive Summary

The Pomodoro Technique has ONE core requirement: work for 25 minutes, then take a 5-minute break. Everything else is optional polish. For a 20-minute demo where planning itself takes time, we need to ruthlessly prioritize the absolute minimum that demonstrates "this is a Pomodoro timer" while leaving complexity for the roadmap to handle.

**Key insight from research:** Most Pomodoro apps are over-engineered. The best ones do basic timing exceptionally well, with minimal friction. "Less friction means more focus" - if starting a timer takes more effort than skipping it, the app's missed the point.

## Table Stakes

Features users expect. Missing = "this isn't really a Pomodoro timer."

| Feature | Why Expected | Complexity | Implementation Time | Notes |
|---------|--------------|------------|---------------------|-------|
| 25-minute countdown timer | Defines Pomodoro (25 min work period) | Low | ~5 min | Core `setInterval()` logic with Date.parse() |
| Start/Stop button | User needs to control timer | Low | ~2 min | Single button, toggle state |
| Visual countdown display | User needs to see time remaining | Low | ~3 min | MM:SS format with zero-padding |
| Timer completion signal | User needs to know when 25 min is up | Low | ~5 min | Alert/notification/sound - pick ONE |
| 5-minute break timer | Defines Pomodoro break period | Low | ~3 min | Same logic as work timer, different duration |

**Total estimated time: ~18 minutes of coding**

### Critical Dependencies

```
Timer logic → Display → Start/Stop control → Completion signal
              ↓
         Break timer (uses same logic)
```

All table stakes features are independent except break timer reuses work timer logic.

### MVP Recommendation

For a 20-minute demo including planning phases, build ONLY table stakes:

1. Single 25-minute countdown
2. Start/Stop button
3. MM:SS display
4. Simple alert() on completion
5. (Optional) 5-minute break mode if time permits

**Ship criteria:** Timer counts down 25 minutes and alerts user when done.

## Nice-to-Have

Features that add polish but consume precious demo time. Each adds complexity without changing "this is a Pomodoro timer."

| Feature | Value Proposition | Complexity | Time Cost | Risk |
|---------|-------------------|------------|-----------|------|
| Auto-cycle work/break | User doesn't manually switch modes | Medium | ~10 min | State management complexity |
| Progress bar visual | Better UX than numbers alone | Low | ~5 min | Distraction from core demo |
| Browser tab countdown | See time without switching windows | Low | ~3 min | Low value in demo context |
| Sound alerts | More noticeable than visual alert | Low | ~5 min | Audio file sourcing, browser permissions |
| Desktop notifications | Modern UX expectation | Medium | ~8 min | Notification API permissions, browser compatibility |
| Pause/Resume | Interruption handling | Medium | ~7 min | Complicates state, breaks "Pomodoro can't be split" rule |
| Reset button | Start over if needed | Low | ~2 min | Minimal value, Start/Stop covers it |
| Long break (15 min) | Official technique: every 4 pomodoros | Low | ~3 min | Requires pomodoro counter |
| Pomodoro counter | Track completed sessions | Low | ~5 min | State tracking, display update |
| Custom time intervals | User sets own work/break lengths | Medium | ~10 min | Settings UI, validation, state management |

### Why These Are Nice-to-Have, Not Essential

**For demo purposes specifically:**
- **Auto-cycle**: Impressive but adds state complexity. Manual mode switching is simpler and demonstrates the concept fine.
- **Progress bar**: Visually appealing but doesn't prove timer works. Numbers are sufficient.
- **Tab countdown**: Useful feature but requires switching focus during demo, odd UX.
- **Sound alerts**: Better than alert() but requires asset management. Visual notification works.
- **Desktop notifications**: Professional touch but permission prompts interrupt demo flow.
- **Pause/Resume**: Violates Pomodoro principle ("Pomodoro can't be split"). Official technique says restart if interrupted.
- **Long break**: Requires tracking 4 pomodoros. Demo won't run that long.
- **Counter**: Tracking feature, not core timer functionality.
- **Custom intervals**: Settings UI is scope creep. 25/5 is the Pomodoro standard.

### Defer to Post-Demo

If building beyond demo:
1. **First add:** Desktop notifications (professional UX)
2. **Then add:** Pomodoro counter + long break (official technique completeness)
3. **Then add:** Custom intervals (user flexibility)
4. **Maybe add:** Pause/Resume (user request, but anti-pattern)
5. **Avoid forever:** See Anti-Features below

## Anti-Features

Features to explicitly NOT build. Common mistakes in this domain.

| Anti-Feature | Why Avoid | What to Do Instead | Research Source |
|--------------|-----------|-------------------|-----------------|
| Task management integration | Scope creep: timer ≠ todo list | Keep timer standalone. If needed later, integrate with existing tools | Multiple reviewers noted "I don't want to manage my whole week in a sub-par app just for a timer" |
| Analytics/Statistics dashboard | Over-engineering for demo context | Simple counter is enough. Real analytics needs database, charting, date ranges | "Most Pomodoro apps are over-engineered" - testing feedback |
| Account system / Cloud sync | Massive complexity for minimal gain | Local storage only. No backend needed for timer | Demo is 20 minutes, no time for auth |
| Multiple simultaneous timers | Violates Pomodoro methodology | Single focused timer only | Technique principle: one task, one Pomodoro |
| Website/App blocking | Feature bloat, platform-dependent | Timer reminds user to focus, doesn't enforce | Separate concern from timing |
| Pomodoro "credits" or gamification | Gimmick that distracts from technique | Let the technique work on its own merit | Research shows time-structured intervals work regardless of points/badges |
| Music/Ambient sound player | Build a timer, not a music player | User can use Spotify/etc separately | Unrelated to core value proposition |
| Team/Collaboration features | Wrong product direction entirely | Pomodoro is personal technique | Massive scope increase |
| Mobile app (for this demo) | Platform complexity | Web-first is universal, works on mobile browsers | 20-minute demo constraint |
| Complex settings panels | "If you need a manual, it's too complex" | Sensible defaults, maybe 2-3 toggles max | Minimal design philosophy: "elegant and easy-to-use" |

### Why These Kill Demos

**Critical insight:** Every feature beyond table stakes:
1. Takes time to plan (reduces demo wow-factor)
2. Takes time to build (risks incomplete demo)
3. Adds explanation burden (confuses audience)
4. Creates failure points (broken features in demo)

**Demo anti-pattern:** "I'll add just one more feature..." then run out of time and ship nothing.

### The "Glorified Timer" Test

One user review: "The price... it's quite pricey for a glorified timer."

**This is the test:** If a feature makes someone say "that's not timer functionality, that's X," it's scope creep.
- Analytics dashboard? That's data visualization.
- Task management? That's project management.
- Account system? That's user infrastructure.
- Music player? That's media software.

**Build a glorified timer. Own it. Ship it.**

## Feature Dependencies

```
PHASE 1: Core Timer (table stakes)
├─ Timer logic (setInterval, state)
├─ Display (MM:SS formatting)
├─ Controls (Start/Stop button)
└─ Completion (alert on 0:00)

PHASE 2: Break Timer (table stakes)
└─ Reuses Phase 1 logic, adds mode switching

PHASE 3: Polish (nice-to-have, post-demo)
├─ Notifications (requires Notification API)
├─ Counter (requires state persistence)
└─ Progress bar (requires Phase 1 timer logic)

PHASE 4: Advanced (probably never for demo)
├─ Custom intervals (requires settings UI)
└─ Long breaks (requires counter from Phase 3)
```

**For 20-minute demo:** Ship Phase 1 completely, Phase 2 if time allows, skip Phase 3-4 entirely.

## Implementation Complexity Analysis

### Low Complexity (< 5 min each)
- Start/Stop button
- Display formatting
- Reset button
- Progress bar HTML
- Browser tab title
- Long break mode

### Medium Complexity (5-10 min each)
- Timer countdown logic
- Auto-cycling between modes
- Desktop notifications
- Pause/Resume state
- Custom interval settings

### High Complexity (> 10 min each)
- Task management
- Statistics/Analytics
- Cloud sync
- Account system
- Any backend integration

**Demo constraint:** Stick to Low complexity only, maybe 1-2 Medium items.

## Confidence Assessment

| Category | Confidence | Source |
|----------|------------|--------|
| Table stakes features | **HIGH** | Official Pomodoro Technique documentation, multiple tutorial implementations |
| Nice-to-have vs anti-features | **HIGH** | User reviews of 11+ apps, implementation tutorials, minimalist timer examples |
| Implementation time estimates | **MEDIUM** | Based on tutorial walkthroughs, not empirical testing |
| Feature dependencies | **HIGH** | Clear from code structure in tutorials |
| Demo time constraints | **HIGH** | Given: 20-minute demo including planning |

## Research Methodology

**Sources consulted:**
1. **Official Pomodoro Technique documentation** (verified table stakes)
2. **11+ Pomodoro app reviews (2026)** (identified common features and user complaints)
3. **4+ implementation tutorials** (JavaScript, Python, React+Electron) (complexity analysis)
4. **GitHub minimalist timer projects** (design philosophy, intentional exclusions)
5. **User reviews and feedback** (anti-patterns, over-engineering complaints)

**Verification approach:**
- Cross-referenced "essential features" across official docs, tutorials, and app reviews
- Identified user complaints about feature bloat to define anti-features
- Used tutorial walkthroughs to estimate implementation complexity
- Checked multiple minimalist implementations to validate "less is more" approach

## Sources

**Official Pomodoro Technique:**
- [Pomodoro Technique - Official Site](https://www.pomodorotechnique.com/)
- [Pomodoro Technique - Wikipedia](https://en.wikipedia.org/wiki/Pomodoro_Technique)
- [Pomodoro Technique - Todoist Guide](https://www.todoist.com/productivity-methods/pomodoro-technique)

**App Reviews & Comparisons (2026):**
- [Top 11 Pomodoro Timer Apps for 2026 | Reclaim](https://reclaim.ai/blog/best-pomodoro-timer-apps)
- [Best Pomodoro Timers in 2026 | Productivity Apps](https://toolfinder.co/best/pomodoro-timers)
- [Top Pomodoro Timer Apps for Enhanced Productivity in 2026](https://focuskeeper.co/blog/best-pomodoro-timer-app)
- [Best 100% Free Pomodoro Apps to Try in 2026](https://www.paymoapp.com/blog/pomodoro-apps/)
- [15 best free Pomodoro apps to try in 2026](https://www.jotform.com/blog/best-pomodoro-app/)

**Minimalist Implementations:**
- [Minimalist Pomodoro Timer | Flocus](https://flocus.com/minimalist-pomodoro-timer/)
- [Flow | A Simple Pomodoro Timer](https://www.flow.app/)

**Implementation Tutorials:**
- [How to build a Pomodoro Timer App with JavaScript](https://freshman.tech/pomodoro-timer/)
- [Create a Pomodoro timer with HTML, CSS, and vanilla JavaScript](https://webdesign.tutsplus.com/create-a-pomodoro-timer-with-html-css-and-vanilla-javascript--cms-108069t)
- [Build a Pomodoro Timer using Python](https://medium.com/@fidel.esquivelestay/build-a-pomodoro-timer-using-python-d52509730f60)

**Open Source Examples:**
- [GitHub - Pomotroid: Simple and visually-pleasing Pomodoro timer](https://github.com/Splode/pomotroid)
- [GitHub - simple-pomodoro: Plain, minimalistic and easy Pomodoro timer](https://github.com/kimborgen/simple-pomodoro)

**Development Insights:**
- [The Pomodoro Technique for Coders: Why Standard Timers Fail](https://super-productivity.com/blog/pomodoro-technique-for-coders/)
- [How I built my Pomodoro Clock app - freeCodeCamp](https://www.freecodecamp.org/news/how-i-built-my-pomodoro-clock-app-and-the-lessons-i-learned-along-the-way-51288983f5ee/)
