---
layout: post
title: "Autopilot Game Platform"
date: 2026-05-30 09:00:00
categories: computer
---

I wanted to see what happens when you give an AI agent a task and get out of the way.

Every 12 hours, an agent picks a game idea, writes the code, tests it in a real browser, fixes bugs, and deploys it to the internet. I don't write any code. I don't review anything. I just get a Telegram message when a new game ships.

That's **game.sinau.dev** — 40+ HTML5 games for kids, running on autopilot.

## The Stack

Nothing fancy:

- **Games:** Single-file HTML with canvas rendering, zero dependencies
- **Builder:** A Python script (`build.py`) that bundles all games into an `index.html` gallery + `manifest.json`
- **Hosting:** Cloudflare Pages, custom domain
- **Source control:** GitHub (`jancuk/hawa-game`)
- **AI Agent:** [Hermes Agent](https://hermes-agent.nousresearch.com) — open-source autonomous AI assistant
- **LLM:** DeepSeek V4 Pro

No backend, no database, no build framework. Just static files on a CDN.

## How a Game Gets Made

Every 12 hours, a cron job kicks off and the agent goes through five steps.

### 1. Pick an idea

The agent picks a game type from a list — memory match, catch/dodge, tap rhythm, maze runner, that kind of thing — and comes up with a theme suitable for young kids. The rule is simple: a 5-year-old must understand what to do within 3 seconds.

### 2. Write the code

A complete single-file HTML game using canvas. Every game follows the same pattern:

```javascript
(function(){
  var btnStart = document.getElementById('btnStart');
  btnStart.addEventListener('click', startGame);

  function startGame() {
    // ... game loop
  }
})();
```

No frameworks. No build tools. One file, done.

### 3. Validate

A Python validator runs through a checklist — correct button listener patterns, IIFE wrapping, no external dependencies, canvas element present, file size under 100KB. If something fails, the agent fixes it and retries.

### 4. Browser test

This is the step most AI pipelines skip. The agent actually opens the game in a real browser and checks: does the page load without JS errors? Does the start button work? Does clicking Play actually start the game? Does the canvas render? Any console errors?

If something breaks, it patches the bug and tries again.

### 5. Deploy

```bash
python3 build.py              # Rebuild gallery
git add -A && git commit      # Commit new game
git push origin main          # Push to GitHub
wrangler pages deploy dist/   # Deploy to Cloudflare Pages
```

Then I get a ping on Telegram. New game, live.

## The Numbers

A few weeks in:

- **41 games** live
- **~2 games/day**
- **Average game size:** 15-20KB
- **Zero manual code** from me
- **Cost:** $0 (Cloudflare free tier, open-source LLM)

## Why I Built This

Most people using AI agents treat them as copilots — tools that help you work faster. But I was more interested in the autopilot question: can an agent just do the whole job? Not "help me write a game" but "write, test, and ship a game without me."

[Sequoia wrote about this recently](https://sequoiacap.com/article/services-the-new-software/) — the idea that the next wave of software companies won't sell tools, they'll sell outcomes. A copilot sells you the tool. An autopilot sells you the work. For every dollar spent on software, six are spent on services. The companies that deliver the service, not the tool, will capture the bigger budget.

game.sinau.dev is a toy version of that thesis. The "service" is game creation. The "outcome" is a working game on the internet. No game engine, no design tool, no developer. Just an agent that ships.

Simple experiment. But it answers a question worth asking: *can AI agents deliver finished work autonomously, on a schedule, with real users?*

Turns out they can.

If you have kids (or are just curious), check out **[game.sinau.dev](https://game.sinau.dev)** on your phone. All games are mobile-friendly with touch controls. Source code is at **[github.com/jancuk/hawa-game](https://github.com/jancuk/hawa-game)**.

This article was written by a human. The games were not.
