---
layout: post
title: "How I Automated a Children's Game Platform with an AI Agent"
date: 2026-05-30 09:00:00
categories: computer
---

What if an AI could autonomously design, build, test, and deploy a new children's game — every 12 hours — with no human intervention?

That's exactly what **game.sinau.dev** is. A platform with 40+ HTML5 games for kids aged 4–8, running entirely on autopilot. An AI agent named **Hermes** handles the entire pipeline: concept, code, validation, git commit, merge, and deploy. I haven't manually written a single game.

Here's how it works.

## The Stack

The architecture is deliberately simple:

- **Games:** Single-file HTML with canvas rendering, zero dependencies
- **Builder:** A Python script (`build.py`) that bundles all games into an `index.html` gallery + `manifest.json`
- **Hosting:** Cloudflare Pages with a custom domain (`game.sinau.dev`)
- **Source control:** GitHub (`jancuk/hawa-game`)
- **AI Agent:** [Hermes Agent](https://hermes-agent.nousresearch.com) — an open-source autonomous AI assistant
- **LLM:** DeepSeek V4 Pro for both creation and auditing

No backend, no database, no build framework. Just static files served by Cloudflare's CDN.

## The Creation Pipeline

Every 12 hours, a cron job fires:

```
hawa-game-creator (every 720 minutes)
├── Model: deepseek-v4-pro via OpenCode
├── Toolsets: terminal, file, web, browser
└── Deliver: origin (Telegram notification)
```

Here's what happens each cycle:

### 1. Concept Generation

The agent picks a game type from a predefined list (memory match, catch/dodge, tap rhythm, maze runner, etc.) and generates a unique concept with a theme suitable for young children. It avoids abstract mechanics — the rule is: *a 5-year-old must understand what to do within 3 seconds*.

### 2. Code Generation

The agent writes a complete single-file HTML game using canvas rendering. Every game follows strict conventions:

```javascript
(function(){
  // IIFE-wrapped game code
  var btnStart = document.getElementById('btnStart');
  btnStart.addEventListener('click', startGame);

  function startGame() {
    // ... game loop
  }
})();
```

No frameworks. No build tools. Just a self-contained HTML file with inline CSS and JavaScript.

### 3. Validation

A Python validator (`validate.py`) checks the game against a checklist:

- Correct button listener patterns (`getElementById` + `addEventListener`)
- IIFE wrapping
- No external dependencies
- Canvas element present
- File size under 100KB

If validation fails, the agent fixes the issues and retries.

### 4. Browser Testing

This is the critical step most AI pipelines skip. The agent actually **opens the game in a real browser** and runs a 5-point E2E check:

1. Does the page load without JS errors?
2. Does the start button exist and is clickable?
3. Does clicking Play actually start the game (overlay hidden)?
4. Does the game canvas render content?
5. Are there any console errors during gameplay?

If any check fails, the agent diagnoses and patches the bug before proceeding.

### 5. Deploy

```bash
python3 build.py              # Rebuild gallery
git add -A && git commit      # Commit new game
git push origin main          # Push to GitHub
wrangler pages deploy dist/   # Deploy to Cloudflare Pages
```

The entire process is autonomous. I get a Telegram notification when a new game ships.

## The Audit Pipeline

Shipping games is only half the battle. What about quality control?

A second cron job runs every 2 days and audits the 6 newest games:

```
hawa-game-audit (every 2 days at 11:00 WIB)
├── Model: deepseek-v4-pro via OpenCode
├── Toolsets: browser, terminal, file, web
└── Audits: 6 newest games, auto-removes buggy + lowest scored
```

The audit agent:

1. Opens each game in a headless browser
2. Attempts to play through the game programmatically
3. Checks for common failure patterns (overlays that don't dismiss, blank screens, broken click handlers)
4. Scores each game on playability
5. **Auto-removes** the lowest-scoring game if it's genuinely broken

This creates a natural selection loop: games that don't work get pruned, and new games take their place. The platform maintains quality without human review.

## Lessons Learned

### What worked

- **Single-file games** are the perfect format for AI generation. No module system, no bundler config, no dependency resolution. One prompt → one file → one game.
- **Browser testing is non-negotiable.** AI-generated code has subtle bugs that pass static analysis but fail at runtime. A game that "looks correct" in code might have an overlay that never dismisses, or click handlers that don't fire on touch devices.
- **Pointer capture for mobile D-pads.** The biggest mobile bug was stuck movement — when your finger slides off a button, `onTouchEnd` fires on the wrong element. `setPointerCapture()` guarantees the `pointerup` event reaches the original button. This pattern should be in every mobile game tutorial.
- **Natural selection > manual curation.** Letting the audit agent remove broken games automatically means the platform self-cleans. I don't need to review 40 games manually.

### What didn't work

- **Vite code splitting with Three.js.** Splitting Three.js and React Three Fiber into separate chunks broke initialization order on mobile browsers — blank white screen. Some libraries just need to be in a single bundle.
- **`html { position: fixed }` on mobile.** This collapsed the entire page layout on touch devices. Always use `body { position: fixed }` instead — `html` is the root element and removing it from normal flow breaks everything downstream.
- **`ReferenceError` is the #1 cause of blank pages.** 90% of the time a page is blank on mobile, it's a JavaScript crash, not a CSS issue. Always check the console before chasing layout bugs.
- **Abstract game mechanics fail.** Games like "sort by size" or "complete the pattern" confuse 5-year-olds. The agent learned to prefer concrete actions: "tap the red balloon", "catch the falling fruit", "pop all bubbles".

## The Numbers

After running this system for a few weeks:

- **41 games** currently live
- **~2 games/day** created (every 12 hours)
- **Average game size:** ~15-20KB (single HTML file)
- **Zero manual code** written by me
- **Platform cost:** $0 (Cloudflare Pages free tier, open-source LLM via OpenCode)

## Try It

If you have kids (or just want to see what AI-generated games look like), visit **[game.sinau.dev](https://game.sinau.dev)** on your phone. All games are mobile-friendly with touch controls.

The source code for the game platform is at **[github.com/jancuk/hawa-game](https://github.com/jancuk/hawa-game)**, and the agent that creates them runs on **[Hermes Agent](https://hermes-agent.nousresearch.com)** — an open-source autonomous AI assistant.

---

*This article was written by a human. The games were not.*
