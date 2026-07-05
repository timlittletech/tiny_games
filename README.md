# Tiny Garden

A small suite of calm, garden-themed games for kids ages 1–7. One HTML file, no dependencies, works on iPad and computers.

## Games

- **Pop the Fluff** (ages 1–3) — Tap floating dandelion seeds to pop them. Slow / Medium / Fast speed selector (choice remembered).
- **Butterfly Friend** (all ages) — Tap a calm butterfly to make it flutter to a new spot.
- **Coloring Garden** (all ages) — Tap regions to fill with color from a 12-color wheel, or stamp stickers (ladybug, bee, butterfly, flower, leaf, star, heart, sun). Tiny shapes are merged into their parent so little fingers can't miss.
- **Count Ladybugs** (ages 4–7) — Count the bugs on the flower and tap the right number.
- **Find the Letter** (ages 4–7) — Tap the flower with the matching letter. Includes spoken letter playback.
- **Story Time** (ages 4–6) — Five short decodable stories, shown three words at a time. Each set is read aloud with a progress bar underneath; the child taps **Next** when ready for the next words. Slow / Medium / Fast reading speed (default Fast). Tap any word to hear it again.

_Garden Puzzle is temporarily rotated out of the lineup (`active: false` in `GAME_ROTATION`); its code remains for a future rebuild._

## Design choices

- Calm but cheerful pastel palette, no flashing animations.
- Gentle Web Audio sine-wave chimes, with a 🔊 mute toggle (preference persists in localStorage).
- Touch-first: swipe between games, big tap targets, locked against pinch-zoom and pull-to-refresh.
- Safe-area aware (works with iPad/iPhone notch); portrait and landscape.
- No external libraries, no network calls, no analytics.

## Parental play timer

The ⏱️ button (top bar) lets a grown-up set a play time (5–30 min) or turn it
off. When the time is up the whole app pauses behind a lock screen and requires
an adult to solve a simple addition problem before setting a new time. The
deadline is stored so reloading the page can't skip it.

## Weekly game rotation

The home screen and carousel are driven by a `GAME_ROTATION` config near the
top of the `<script>` in `index.html`, so you can swap games in and out each
week (and add new ones over time) without deleting their code:

```js
const GAME_ROTATION = [
  { key: 'pop',       active: true },
  { key: 'butterfly', active: true },
  { key: 'color',     active: false },        // hidden this week
  { key: 'letter',    weeks: [50, 51, 52, 1] }, // only shows those ISO weeks
];
```

- `active: false` hides a game; `active: true` (the default) shows it.
- `weeks: [...]` shows a game **only** during those ISO week numbers (1–53),
  which overrides `active` — handy for queuing a seasonal "game of the week".
- Games appear in the order listed. A game left out of the list is still shown.
- If the config would hide *every* game, it's ignored (fail-safe).

To add a brand-new game: add its `<button class="card" data-game="...">` on the
home screen and its `<section class="game" data-game="...">` panel in the
carousel, then add a matching entry to `GAME_ROTATION`. Redeploy by pushing to
`main`.

## Run locally

Just open `index.html` in any modern browser.

## View on the web

If GitHub Pages is enabled for this repo, the live game is at:
**https://timlittletech.github.io/tiny_games/**

## License

MIT
