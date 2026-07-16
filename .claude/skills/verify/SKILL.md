---
name: verify
description: Build/launch/drive recipe for verifying changes to the Melomaniac Studios site (static single-file HTML app).
---

# Verifying melomaniacstudios

Static site, everything lives in `index.html` (CSS + markup + router + quiz + WebGL hero in one file). No build step.

## Launch

```bash
python3 -m http.server 8734 --bind 127.0.0.1   # from repo root, run in background
# → http://127.0.0.1:8734/index.html
```

## Drive

Playwright (chromium) headless works. If only the full browser is downloaded (not headless-shell), launch with `chromium.launch({ channel: 'chromium' })`.

Gotchas learned the hard way:

- `html { scroll-behavior: smooth }` makes every scroll (including the router's `scrollTo(0,0)`) an animation over ~1s. Don't read `scrollY` after a fixed wait — poll until it's unchanged across two reads 300ms apart.
- The quiz modal is `#quiz-mo`, hidden via `.off` (`display:none`). It's `position:fixed`, so `offsetParent` is always null — check `classList.contains('off')` / computed display instead.
- Wait ~1.5s after load for the hero to settle (`body.hero-live` or `body.hero-poster` hides `#loading-cover`).
- Hash router: pages are `.page`/`.page.on` (display none/block). Routes: `''`, `record-label`, `sound-design`, `melomania`, `manifold`, `early-access`; `#frequency` opens the quiz modal and replaces the hash; unknown hashes that name an element inside `#page-home` (e.g. `#labs`) are in-page anchors.

## Flows worth driving

- Home → click "Labs" pillar (`a[href="#labs"]`) → Labs band at viewport top, `page-home` still on.
- Direct load `/#labs` → straight to Labs band.
- Each page route shows its page at scrollY 0; `#frequency` opens `#quiz-mo`.

## Scroll-restoration history (mitigated)

Browser-back from a scrolled position used to strand mid-scroll (~y=2379 at 1400×900): Chromium snapshots the previous history entry's scroll offset mid-flight of a smooth anchor animation, then its restoration smooth-scroll races the router's scroll-to-top. Mitigated in the router by `history.scrollRestoration = 'manual'` plus an instant (non-smooth) scroll-to-top on page-route navigations, re-asserted on the next frame. In-page anchor scrolls stay smooth. If back/forward scroll behavior regresses, check those lines first — and note the race is timing-sensitive (instrumentation such as wrapping `window.scrollTo` can flip the outcome, so reproduce without instrumentation before concluding anything).
