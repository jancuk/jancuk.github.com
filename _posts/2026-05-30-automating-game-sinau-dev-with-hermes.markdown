---
layout: post
title: "Autopilot Game Platform"
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
