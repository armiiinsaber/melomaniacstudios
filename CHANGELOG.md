# Changelog

All notable changes to the Melomaniac Studios site rebuild.

## [Unreleased]

### Added — generative hero (first pass)
- Repository initialized as a standalone static site (no more Webflow embeds).
- `index.html` — single-file standalone build.
- Design tokens wired to the `DESIGN.md` palette (`--green` #B8F06E, warm ivory paper,
  warm ink, hairline).
- Typography: Fraunces (variable, opsz axis) for display + tagline italics; Inter for UI.
  No monospace, no Bebas Neue.
- Hero section:
  - Full-viewport Three.js "sound-form" on warm ivory (`--paper`).
  - Built from 8/10/12/16-fold star polygons and radial spokes (rosette + girih-inspired
    geometry, rendered as pure math — never named as such in-page).
  - Ink hairlines against paper with green traces woven at ~12% of the line work; one
    green concentric hairline and one small radial accent, per the "one confident green
    moment per viewport" rule.
  - ~280-particle drift field (12% green), gentle z-shimmer.
  - Seeded per load from date+hour+minute → fold count, chord steps, rotation phase,
    accent placement all vary; slow visible evolution over ~20–30s.
  - Cursor perturbation on rotation; passive on touch.
  - `prefers-reduced-motion` → single static poster frame, no rAF.
  - DPR capped at 2; assembly scales/offsets by aspect for portrait, tablet, wide.
- Overlaid content per spec: eyebrow ("Melomaniac Studios · Toronto"), stacked wordmark
  (roman + italic Fraunces at display scale), tagline "A house for sound.", quiet scroll cue.
  Nothing else in the hero.
- `::selection` → green fill, ink text (site-wide signature).
- `_reference/` — the old Webflow head+body preserved for functionality port
  (Supabase auth, Frequency quiz data, Manifold WebGL).

### Fixed — hero robustness (second pass)
- **Vendored three.js.** Downloaded `three.module.min.js` r161 into `/vendor/`
  and switched the hero to `import * as THREE from './vendor/three.module.min.js'`.
  The site's centerpiece no longer depends on a third-party CDN being reachable,
  which also matters once we're on GitHub Pages. (Root cause of the first-pass
  black-screen: three.js dropped the UMD `three.min.js` build starting r150, so
  the CDN URL 404'd and the piece never initialized.)
- **Real SVG poster fallback.** Replaced the "three.js failed to load" text stub
  with an inline SVG poster (concentric ink hairlines, ink star polygons at
  {12/5}, {10/3}, {12/5}, {8/3}, radial spokes, ONE continuous green {12/5}).
  The poster renders synchronously so it's on paper before WebGL initializes,
  fades out on the first successful WebGL frame, and stays put if the module
  import or WebGL context ever fails — no raw error string ever appears in
  the composition.
- **Reduced-motion → poster only.** No longer light up rAF; the static SVG is
  the entire experience.
- **Green as one continuous line.** Replaced the fragmented green (a segments
  star + green ring + green radials) with a single `THREE.LineLoop` drawn from
  a coprime `{n/step}` pair (gcd=1 → one closed stroke hitting every vertex).
  Kept one very quiet green hairline circle at the same radius to ground the
  trace, and dropped particle-green share to ~8% so the LineLoop carries the
  ration. Reads as one living green current through the ink drawing.

### Changed — hero, third pass (fluid + tempo)
- **20% slower across the board.** All animation now runs off a single
  `SPEED = 0.80` multiplier applied to a derived "slow time" `T = t * SPEED`.
  Layer spins, assembly drift, cursor easing, ink dust, fluid noise, fluid
  wave, and green-line breath all pass through `SPEED`, so tuning stays
  internally proportional. CSS `scrollpulse` bumped 2.8s → 3.5s to match.
- **New green fluid-form** — a `THREE.Points` field with a `ShaderMaterial`:
  simplex-noise flow field (3 offset samples, time-scrolling coord for the
  "drifts like smoke" feel) plus a planar traveling wave that modulates both
  displacement amplitude and per-particle alpha. Green-primary (~85 %), ink
  minority (~15 %), sharp print-like discs (tight `smoothstep` alpha, `discard`
  at edges — no glow). `NormalBlending` with `depthWrite: false` — never
  additive on ivory. Seeded per load: flow direction, wave direction/frequency/
  speed, tilt, and z-offset all vary. Sits inside the assembly so it inherits
  the same offset/scale as the architecture, but has its own tilt so it visibly
  crosses through the rosette plane.
- **Particle-count tiers** (drop first if it chugs): 3200 ≥1400w, 2400 ≥1000w,
  1500 ≥640w, 900 below.
- **Green rebalanced.** The single-continuous LineLoop stays but its opacity
  now breathes on a very slow (~114s) cycle between ~0.10 and ~0.42, so it
  reads as a rising accent rather than the primary green source. The old green
  ring is gone. Ink drift dust is 100% ink now — total green budget lives in
  the fluid + the LineLoop's occasional swell.
- **SVG poster** now includes a seeded green-primary particle scatter along
  the seeded flow direction, so both the WebGL cross-fade and the reduced-
  motion display carry the same "fluid" DNA.

### Rebuilt — hero, fourth pass (one shared wave field)
- **Organizing principle change: physics, not decoration.** The piece is a
  simulation now — every element samples ONE shared wave field. No element
  moves to a private clock.
- **The field.** Three seeded stationary emitters, each radiating a spherical
  wave `u_i = A_i · sin(k·r − ω·t + φ) / (1 + α·r)`. Superposition is linear,
  so real constructive/destructive interference happens naturally. Wave speed
  `c = ω/k ≈ 1.33` (chosen so wavefronts cross the scene in a few seconds).
  Emitter positions, ω, A, φ are all seeded per load.
- **Standing wave** (Chladni-style): `sin(k_x·x)·sin(k_y·y)` term with its
  own seeded `k`s; amplitude rises and recedes on a very slow (~180 s) cycle
  so nodal grids emerge and dissolve — never stuck, never absent.
- **Mouse = emitter, not magnet.** Cursor is a fourth persistent source.
  Screen → world plane → assembly-local space; amplitude fed by motion,
  decays when idle (~2 s to zero). Phase reference shared with the base
  emitters so it *interferes* with the field cleanly instead of sitting on
  top. Removed the old assembly-follows-cursor rotation.
- **Star geometry — gone.** Removed every star polygon (`starChords`,
  `starOneLine`, `girihBand`, `radials`, `greenTrace`, `greenRing`), the
  central 8-fold rosette, the ink dust drift particles, and the girih band.
  Only the quiet architecture whisper remains: three faint hairline circles.
- **Circles now read the field.** Same equation, mirrored in JS at 128 verts
  per circle. Subtle radial + z displacement (max ~5 % of radius) — lines
  breathe with the same physics driving the particles.
- **Particles: print-dot scale, green-first.**
  - Count tiers ~2× the previous pass: 7000 / 5000 / 3000 / 1800 by viewport
    width. Vertex shader is cheap (~80 ops, no simplex noise), 60 fps holds.
  - ~92 % green, ~8 % ink for weight — the piece reads unmistakably green
    on ivory now.
  - Fragment size ~1–3 CSS px depending on field magnitude. Sharp `smoothstep`
    + `discard` at edges → print-like, no glow.
  - Displacement along the vector field: waves *visibly* travel through the
    cloud. Alpha and size gated by `smoothstep(|scalar_field|)` so
    interference bands read as visible bright/dim bands moving with the field.
- **Load-sequence pop, fixed.** SVG poster rebuilt to the same composition
  (seeded green scatter shaped by the frozen `t = 0` field snapshot + faint
  hairline circles — no stars). WebGL renders its first frame while the
  canvas is still at opacity 0; a double-`requestAnimationFrame` guarantees
  the frame is presented before the CSS crossfade kicks in. Composition
  matches on both sides → no visible jump.
- Kept unchanged: single `SPEED = 0.80` knob (`T = t * SPEED` drives every
  timing), seeded per-load variation, reduced-motion → SVG poster only,
  60 fps particle-count tiers.

### Rebuilt — hero, fifth pass (110-BPM beat physics + first-paint contract)
- **First paint is now the finished composition.** The load-then-swap flow
  is gone. New contract:
  - `<link rel="modulepreload" href="./vendor/three.module.min.js">` in
    `<head>` overlaps the module fetch with HTML parsing.
  - `<div id="loading-cover">` is an ivory overlay at `z-index: 999` that
    covers the entire viewport from HTML parse. `html` and `body` also
    paint `--paper` so the blank moment is truly blank ivory — no font
    swap, no partial layout, no poster peek.
  - `initHero` **pre-renders the first full frame in the same task** as
    the reveal: `frame(T0)` → `onFirstFrameReady()`. The cover is removed
    on the same paint that lands the finished composition on screen.
    No opacity transition anywhere on the swap — it's a hard reveal.
  - Poster is now the FALLBACK only (WebGL init throws / module import
    fails / 3 s timeout hits) and the reduced-motion display. Never a
    loading state.
  - The clock is **pre-advanced** by `2 · BEAT_PERIOD + 0.1 s ≈ 1.19 s`
    before that first render, so several beats are already in flight when
    the page first paints — the piece is already alive.
- **110 BPM beat physics.** The wave field is now beat-driven, on a real-
  time clock (BPM locked; SPEED only scales physics tau).
  - `BEAT_PERIOD = 60 / 110 ≈ 545.45 ms`.
  - Each beat, every emitter fires a **wavepacket**: a Gaussian ring
    `exp(−(r − c·τ)² / 2σ²) · cos(k·r − ω·τ + φ) · exp(−τ / 0.32)` at
    `c = 2`, `σ = 0.5`. Three most recent beats are tracked as uniforms
    (`uBeatTimes`, `uBeatAmps`), so up to three concentric rings per
    emitter are in flight simultaneously. Between beats no new energy
    is added — the field decays toward rest, then re-energizes on the
    next hit. Pulse/rest contrast reads like a physical speaker cone.
  - **4-beat bar structure.** Every 4th beat (indices 0, 4, 8, …) is the
    downbeat, amplitude `1.35`; other beats `0.85`. About a 1.6× ratio,
    audible without being cartoonish.
  - **Global kick envelope** (attack ~30 ms, exponential decay ~300 ms)
    modulates particle size (`+0.4×` peak) and alpha (`+0.5×` peak) on
    every hit — the transient "hit" the user asked for. Downbeats peak
    at `0.30`, others `0.18`.
- **Mouse layered on top unchanged.** Still a continuous sinusoidal
  emitter with amplitude fed by motion, `0.985` per-frame idle decay.
  Same time reference as the base field so it INTERFERES with the beat
  waves cleanly.
- **Circles keep sampling the field.** JS mirror `beatContribJS` mirrors
  the shader beat contribution exactly; circle vertices deform against
  the same 3-beat superposition each frame.
- **SPEED knob** still scales physics tau (propagation speed, ring decay,
  Chladni amplitude drift, kick envelope width, assembly drift) — the
  "decay/travel feel" knob. BPM stays at 110.
- **Chladni** kept as background pattern at reduced amplitude (0.10 peak
  vs 0.20 before) — subtle nodal grid underneath the beats.

### Fixed — hero, sixth pass (cloud is always visible)
- **Existence separated from excitation.** In the previous pass, particle
  alpha and size were both gated by `smoothstep(|field|)`, so on a still
  field a large fraction of the cloud dropped to ~14% of baseline —
  effectively invisible. Only zones where a wavepacket happened to be
  passing were lit. On page load this read as "empty page with a corner
  of dots."
- **New model — baseline + boost.**
  - `aAlpha` and `aSize` are the per-particle *resting* values. Every
    particle is drawn every frame in the seeded composition, with no
    field gating on visibility.
  - Excitation (`smoothstep(0.05, 0.6, |scalar_field|)`) *only adds* on
    top: alpha × up to `+140 %` at wavefront peaks (clamped to 1.0 in
    the fragment output → peaks max at full opacity, matching the spec
    "up to ~1.0 alpha at wavefronts"), size × up to `+55 %`.
  - Kick boost still layered on top (`+50 %` alpha, `+35 %` size at
    downbeat peak).
- **`aAlpha` range retuned** from `0.45–0.95` to `0.35–0.55` — this is
  now the actual resting alpha per particle, matching the "~0.35–0.5"
  target in the spec. Mean baseline ≈ 0.45.
- **SVG poster mirrors the new model.** `baseAlpha` (0.35–0.55) is
  every dot's rest alpha; the frozen field snapshot only boosts on top.
  So the fallback and reduced-motion display also show the full cloud
  with excited regions brighter — poster and live piece agree at rest
  and under excitation.
- Everything else exactly as before: 110 BPM beat clock, downbeat
  accent, mouse-as-emitter, `SPEED = 0.80` physics knob, first-paint
  contract, poster-as-fallback-only.

### Fixed — hero, seventh pass (mathematically seamless beat transitions)
Three separate discontinuity sources produced the "cut" at every beat.
Each one traced to its root and fixed.

- **Wave contribution wasn't causal.** The Gaussian ring
  `exp(−(r − c·τ)² / 2σ²)` is symmetric around the wavefront, so at
  `τ = 0` a particle at `r > 0` already saw a nonzero value — the ring's
  "leading edge" leaked ahead of the wavefront. So every new beat
  instantly imprinted amplitude on every particle at once = pop.
  **Fix:** genuine causal wave.
  ```
  τ_local = τ − r/c                                (time since arrival)
  if τ_local < 0 → 0                              (wave hasn't arrived)
  attack     = 1 − exp(−τ_local / 0.03)           (~30 ms smooth attack)
  localDec   = exp(−τ_local / 0.20)               (ring shape behind front)
  globalDec  = exp(−τ / 0.35)                     (beat lifetime)
  u          = A·βₖ·attack·localDec·globalDec·cos(k·r − ω·τ + φ) / (1+α·r)
  ```
  At the wavefront `τ_local = 0` so `attack = 0` → u is 0 on both sides of
  the arrival boundary. C0-continuous everywhere.
- **Kick envelope was single-beat.** Previous code used
  `attack(τ₀)·decay(τ₀)·peak` on `beatTimesArr[0]` alone → at every beat
  boundary `τ₀ = 0` → `attack = 0` → the kick collapsed. **Fix:** sum
  the envelope over all 4 tracked beats:
  ```
  env(t) = Σₖ Aₖ · (1 − exp(−τₖ/0.03)) · exp(−τₖ/0.35)
  ```
  Each new beat enters the sum with `attack = 0` growing smoothly; the
  old beats continue their own smooth decay. C∞-continuous through beat
  boundaries. Peaks bumped (`0.30 → 0.55` down, `0.18 → 0.30` reg) to
  compensate for the now-shared amplitude across beats.
- **Slot recycling dropped nonzero amplitude.** With 3 tracked beats
  and `τ_decay = 0.32 s`, the oldest slot at recycle time held
  `τ_eff ≈ 1.31 s` → envelope `exp(−1.31/0.32) ≈ 1.7 %` = a visible
  1.7 % pop. **Fix:** raise to **4 slots** + `T_GLOBAL = 0.35 s`. At
  recycle `τ_eff = 4·p·SPEED ≈ 1.744 s` → envelope `≈ 0.7 %`, safely
  under the 1 % threshold the user asked for. `uBeatTimes` /
  `uBeatAmps` upgraded `vec3 → vec4`; shader main() loops the 4 beats
  per emitter (12 wave calls per particle); JS mirror + kick sum
  match.
- **No per-beat re-randomization audit.** Verified: `emitters`
  (positions/ω/A/φ), particle `pos/aSize/aAlpha/aColor`, `chladniKx/Ky`,
  `mouseFreq` are all set once at load and never touched. Only
  `uBeatTimes / uBeatAmps / uKickBoost / uChladniAmp / uMouseAmp /
  uMousePos / uTime` mutate per frame — every one continuous.
- **`CLOCK_PREADVANCE`** bumped `1.19 s → 2.33 s` (4·p + 0.15 s) so
  first-paint has all 4 slots filled with a fresh downbeat and 3 older
  rings mid-propagation — matches steady state exactly, no early-frames
  thinness.
- **SVG poster** rewritten with the same causal 4-beat sum at
  `T_SNAP = 2.33 s`, so poster field and first live frame agree.

### Added — house index + labs featured (per DESIGN.md §5)
- **Hero approved and FROZEN** — nothing under `.hero` was touched.
- **DESIGN.md updated**: Toronto stripped from the eyebrow spec and the
  footer spec. Added an explicit rule: "no city mentions anywhere in
  house-level copy."
- **THE HOUSE** — editorial pillar index on paper.
  - Oversized Fraunces numerals (`clamp(4.5rem, 9vw, 10rem)`) as
    graphic marks, pillar names in display serif
    (`clamp(2.4rem, 5.5vw, 6rem)`), one-liners in small Inter, hairline
    rules between rows (nothing else — no cards, no glow).
  - Data-driven: `PILLARS` array in JS → rows rendered by a template.
    Adding a 5th pillar means one more object.
  - Four seeded pillars: `01 Record Label / Independent. Artist-first.`,
    `02 Sound Design / Sonic identity for brands and spaces, zero to one
    hundred.`, `03 Melomania / Original music, composed per night.
    Invite only.`, `04 Labs / The experimental wing.` Each links to its
    eventual pillar page hash (pages not built yet); `04` links to
    `#labs` which does exist.
- **LABS FEATURED** — full chartreuse-band section (`--green`) directly
  after The House.
  - Ink-on-green throughout — the signature high-contrast pairing. This
    is the ONE loud accent moment on the page; everything around stays
    quiet paper.
  - Lead: "The newest wing of the house." in display serif.
  - 3-column grid on ≥ 900 px, single-column stack below.
  - Data-driven `LABS` array: `01 Manifold / Your music, made visible. /
    Launch`, `02 Frequency / Find yours in four questions. / Find yours`,
    `03 Noted / Talk to your mix. / Launch (→ noted.melomaniacstudios.com)`.
    Room to add a 4th.
  - Each experiment: number, name (serif), one-liner, CTA with arrow
    across a hairline. Hover slides the arrow right — the only motion.
- Shared `.section` / `.section-inner` / `.section-eyebrow` framework
  scoped for reuse across pillar pages, Early Access, footer later.
- Accessibility: pillar/lab links keyboard-focusable with ink-outline
  focus rings; hover animations disabled under
  `prefers-reduced-motion: reduce`. HTML escapes in the data builder.

### Added — the house cut + bespoke wordmark + Labs redesign
Two upgrades on approved House. Hero body (canvas, physics, first-paint
contract) remained frozen — only its wordmark HTML was swapped by
explicit request.

**Proprietary type instance — "the house cut"**
- Fraunces now loaded as a **full variable font** including the `SOFT`
  and `WONK` custom axes: `Fraunces:ital,opsz,wght,SOFT,WONK@…`.
- One locked instance — **the house cut** — applied everywhere the
  display serif appears via `var(--house-serif-mods)` (SOFT 40, WONK 0).
  Slightly softer terminals, no idiosyncratic swashes — more minimal and
  editorial than Fraunces's default.
- Documented in `DESIGN.md` under a new "The house cut" heading (§3)
  with axis values + CSS pattern, so it can't drift.
- Applied to: hero wordmark SVG, hero tagline, `.pillar-num`,
  `.pillar-name`, `.pillar-arrow`, `.labs-lead`, `.labs-name`,
  `.labs-cta-arrow`.

**Bespoke SVG wordmark (hero + future footer)**
- Wordmark now ships as inline SVG in `<h1 class="hero-wordmark">` —
  two `<text>` lines ("Melomaniac" roman, "Studios" italic) in the
  house cut, plus one hand-drawn bespoke path: a signature hairline
  rule that runs below "Melomaniac" and hooks down toward "Studios".
  This is the honest first-pass bespoke element.
- Accessible text fallback via `.wordmark-fallback` (visually hidden).
- **Follow-up note in DESIGN.md**: a fully flattened-to-paths version
  with hand-modified letterforms needs a font-to-paths tool (not
  runnable inline). Logged so it can't drift into "we forgot."

**Labs redesigned — no numbers, live previews**
- Killed the numbered 3-card grid entirely.
- New layout: **left column = product names** stacked in the house cut
  (`clamp(2.4rem, 5vw, 5rem)`), no numbers. **Right column = live
  preview canvas** running the same wave-field engine as the hero.
- Hover / focus on a name switches the preview to that product's
  parameter preset (opacity dip ~280 ms covers the geometry rebuild).
- **Auto-cycle every 6 s** when idle, interrupted by hover/focus.
- **Ink particles on green**: the labs canvas is transparent so the
  chartreuse band shows through; every particle is ink for the
  signature high-contrast pairing. The green band stays the loud
  accent moment.
- **Data-driven**: `LABS[]` now includes `enginePreset`; adding a
  future product is one entry — name, line, cta, href, preset.
- Mobile (≤ 900 px): grid stacks, canvas below names (order: 2).
  No swipe interaction yet — MVP simplification.
- Focus states: keyboard-focusable names, ink outline ring, animated
  ink bar sliding in on the left to mark the active row.

**Labs preview engine (isolated instance)**
- Own `<canvas>`, own renderer (`alpha: true` → transparent), own
  scene, own beat clock, own SPEED 0.80. Runs independently from
  hero — a Labs failure can't affect the hero.
- Shader source duplicated verbatim from hero's `FLUID_VS/FS` (kept
  hero function untouched, as it must). Same causal 4-beat wavepacket
  physics, same summed kick envelope, same existence/excitation model.
- Three presets: 
  - **Manifold** — filled sphere of 3200 particles + 3 emitters inside
    → reads as a form deforming to the field.
  - **Frequency** — 4-quadrant particle clusters (3500 particles) + 3
    emitters: a "question" pair (top, same freq/phase) and an "answer"
    (bottom, opposite phase) so beats pulse Q → A rhythm.
  - **Noted** — horizontal band (5.6 × 1.0 × 0.5) of 2800 particles + 3
    emitters along the horizontal → reads as a waveform strip.
- `ResizeObserver` on the preview element keeps the canvas in sync
  when the panel changes size.
- Clock pre-advanced ~2.33 s so first paint of a preset already has 4
  beats in flight (matches hero's first-paint contract).

### Added — full site build (pillar pages, Manifold, quiz, waitlist, auth, footer)
The homepage was approved and locked; this pass builds out the rest per
DESIGN.md §5, porting the interactive functionality from `_reference/`
into the new design system.

**Router.** Hash-based (adopted from `_reference/`). `#` → home,
`#record-label` / `#sound-design` / `#melomania` / `#manifold` /
`#early-access` → the respective pillar page. `#frequency` opens the
quiz modal and stays on home. Router hides the loading cover on direct-URL
loads of non-home pages so pillar pages don't sit behind ivory while
hero WebGL is booting.

**Record Label** — Independent. Artist-first. Armin Saber's Spotify /
Apple Music / YouTube / SoundCloud pills; "Discover your song →" CTA
opens the Frequency quiz modal.

**Sound Design** — Sonic identity for brands and spaces, zero to one
hundred. Capabilities list, selected client chips (Porsche, Leyla
Kashani, Haash & Co.), contact email.

**Melomania (dark inversion)** — full `--ink` background, `--paper`
text, green accents that may glow (per DESIGN.md — "on light pages it
never glows"). Event card with the May 2, 2026 date and RA link (no
city per the no-city-in-house-copy rule).

**Manifold** — full-viewport WebGL visualizer ported from `_reference/`.
Dark ground kept; UI chrome (title, buttons, labels) uses the house cut
+ Inter, dropping the old Bebas / DM Mono per DESIGN.md §5. Same audio
reactivity (bass/mid/high band decomposition, deforming sphere, wireframe,
three concentric rings with lit pointlights). Dynamic-imports the same
vendored three.js the hero and Labs use — no duplicate three.js payload.
rAF loop skips render when the page isn't active.

**Early Access** — Waitlist form (first/last name, email) posts to the
same Supabase table + confirmation edge function as `_reference/`.
"You're in" panel on success.

**Frequency quiz modal** — full 4-question flow, 15-song catalog, scoring
logic unchanged from `_reference/`. Result card in the new type system
(house-cut serif for song title, italic Fraunces for the reason line),
Spotify link.

**Auth modal** — Sign up / Log in tabs, Supabase-backed. `_reference/`'s
signup writes a `profiles` row on account creation.

**Footer** — quiet: wordmark (italic Fraunces), Contact (mailto),
Instagram, Early Access. No city.

**Pillar-page framework** — shared `.pillar-nav` (back link + Early
Access shortcut), `.pillar-hero`, `.pillar-body`, `.pillar-section`,
`.pill-row` / `.pill` / `.pill.fill`, brand chips, event card. All in
the house cut with hairline-only borders per DESIGN.md §3.

**Supabase** loaded from CDN (`onerror` graceful degrade — if it fails,
auth and waitlist just no-op; the rest of the site keeps working).

### Pending review
- Serving locally at http://127.0.0.1:4200 — site is navigable end-to-end.
  Awaiting founder review before GitHub Pages + custom-domain setup.

### Next
- The House editorial index (four pillars as equals).
- Labs featured spotlight (allowed the boldest chartreuse band on the page).
- Pillar pages restyled; Melomania dark inversion allowed.
- Early Access restyled; port waitlist + Frequency + Manifold logic from `_reference/`.
- GitHub Pages + custom domain once the full site is approved.
