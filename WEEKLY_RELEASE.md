# Tiny Garden — Weekly Game-Release Process

A pragmatic operating manual for shipping one new kids' game per week on the
single static `index.html` hosted on GitHub Pages. Builds on the existing
`GAME_ROTATION` config, the `game-activated` event contract, and the
`python -m http.server` preview harness already in the repo.

> Draft plan generated to kick off the "new game each week" workstream. Review
> and adjust before adopting.

## 0. What the repo already gives us

- **One file, no build step.** Styles, markup, and all game JS live in `index.html`.
- **Two-part game anatomy, joined by `data-game`:**
  - A **home tile** — `<button class="card" data-game="KEY">` inside `#cards`.
  - A **carousel panel** — `<section class="game" data-game="KEY">` inside `#track`.
- **Per-game JS is a self-contained IIFE** that subscribes to a global event:
  ```js
  document.addEventListener('game-activated', (e) => {
    if (e.detail.game === 'KEY') start(); else stop();
  });
  ```
  The game runs only when its panel is active and **must tear down its
  loops/timers when another game (or `'none'`) is activated.** This is what makes
  rotation cheap: a hidden game never runs.
- **`GAME_ROTATION`** drives visibility and order; `applyRotation()` runs at
  startup, removes off panels/cards, reorders survivors, and has a fail-safe (if
  the config would hide every game it's ignored). `isGameOn()` supports
  `active: true/false` or a `weeks: [ISO weeks]` array that overrides `active`.
- **Deploy = push to `main`** (`.github/workflows/static.yml` auto-deploys).
- **Preview:** `.claude/launch.json` runs `python3 -m http.server 8971`.

## 1. Content pipeline: idea → built → in repo

1. **Idea captured** in the backlog (§6) with a proposed `key`, age band, one-line mechanic.
2. **Built on a branch** — new card + panel + IIFE + `GAME_ROTATION` entry (§2).
3. **QA'd locally** against the harness + checklist (§4).
4. **Scheduled + merged to `main`** (§3, §5) → auto-deploys.

**Stay single-file for now.** No build step is the product's value; splitting into
modules would force a bundler or a hand-rolled global contract. Not worth it at
6–15 games. Mitigate growth with strict per-game section-comment banners and by
keeping all `GAME_ROTATION` edits at the top. **Split trigger:** when
`index.html` passes ~250 KB or ~25 games, extract per-game JS into `games/KEY.js`
as classic `<script src>` tags (still no bundler) and formalize a tiny
`window.TG = { Sound, onActivate }` surface.

## 2. Authoring checklist — "add one game" template

Pick a lowercase `key` used identically in all three places.

1. **Home tile** — append inside `#cards`.
2. **Carousel panel** — append inside `#track` with a `.game-label`, optional
   `.bg-svg` backdrop, and a `KEY-`prefixed play area.
3. **Game logic IIFE** — append inside `<script>`; implement `start()`/`stop()`
   and subscribe to `game-activated`.
4. **Shared systems** — use the global `Sound` (`Sound.pop/correct/wrong/happy/
   gentle/tone`); it's unlocked on first pointerdown, no per-game init.
5. **Register** — add `{ key: 'KEY', active: true }` to `GAME_ROTATION`.

**Non-negotiable rules:** `stop()` must fully halt the game (cancel every
rAF/interval/timeout, remove spawned DOM); prefix every `id`/class with `KEY-`;
reuse the `.bg-svg` backdrop; match an existing age-band string.

## 3. Scheduling & rotation strategy

**Model: evergreen shelf + a "Game of the Week" spotlight.** Don't rotate
everything out — keep the strong evergreens always visible, add one new game per
week to the **top** of the list (it becomes the de-facto spotlight), and cap
visible tiles at ~8–10. Over the cap, retire the oldest non-evergreen by flipping
it to `active: false` (code stays; instantly re-enableable). Pre-schedule
seasonal games with `weeks: [50,51,52,1]`. A game left out of `GAME_ROTATION`
entirely still shows — to hide, list it explicitly with `active: false`.

## 4. Testing / QA before shipping

Run `python3 -m http.server 8971`. Per game (new **and** neighbors): tile
renders + opens correct panel; core mechanic + win feedback works; **teardown**
(swipe away and back → no duplicate nodes / runaway timers; Home stops it);
sound toggle respected; no console errors. Device matrix: portrait **and**
landscape, iPad touch (finger-sized targets), small phone width, regression pass
on the previous spotlight. To test a seasonal week, temporarily hard-code
`const week = 51;` in `applyRotation()` — revert before commit.

## 5. Deployment & rollback

`main` is production (auto-deploys); never build on `main`. One branch per game
(`game/KEY`), QA locally, merge to `main`. **Rollback, two speeds:** fast = flip
`active: false` and push (game vanishes, code untouched); full = `git revert
<sha>` (removes card + panel + IIFE + rotation line together, since they're one
commit). Worst case is ~1–2 min of a bad game live; no staging infra warranted.

## 6. Cadence & backlog

Keep a `GAMES.md` backlog + schedule table in the repo (Week / Key / Title / Age
/ Status / Notes). Status vocabulary maps to config: Live = `active:true`,
Retired = `active:false`, Scheduled seasonal = `weeks:[…]`. Weekly ritual (~15
min): pull top idea → build on branch → QA → merge (Friday for a weekend game) →
update status → retire oldest non-evergreen if over cap. Keep 2–3 games
pre-built/scheduled as buffer.

## Summary of the weekly loop

1. Pull top idea from `GAMES.md`.
2. `git checkout -b game/KEY`.
3. Add card + panel + IIFE + `GAME_ROTATION` entry (§2).
4. `python3 -m http.server 8971`; run QA incl. iPad + orientation (§4).
5. Merge to `main` → auto-deploys.
6. Update `GAMES.md`; retire oldest non-evergreen if over the cap.
7. If anything's wrong: flip `active:false` and push, or `git revert`.
