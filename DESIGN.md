# DESIGN.md — Melomaniac Studios rebrand
## "The Sound Maison"

This document is the binding design spec for the full rebuild of melomaniacstudios.com. Read completely before writing any code. Where this spec and old code disagree, this spec wins.

ONE THING SURVIVES from the current site: the brand green, chartreuse `#B8F06E`. It is the permanent, non-negotiable signature color of Melomaniac Studios and everything is built around it. What IS retired is the context that made it generic: the black terminal ground, Bebas Neue, DM Mono, and the "underground electronic" aesthetic. The rebrand's core move: **the same green, in a completely different world** — light, editorial, expensive.

---

## 1. Brand concept

Melomaniac Studios is a **house** — a parent company of creative sound ateliers, presented the way a luxury maison presents its métiers. Four pillars today (Record Label, Sound Design, Melomania, Labs), built to grow to five, six, more. The site must feel like: a gallery, a design house, an institution-in-the-making. It must NOT feel like: a techno collective, a startup landing page, a terminal, a rave flyer.

Tone in one line: **minimal, clean, editorial — with pop-art confidence.** Vast light space, enormous type, one loud color used with discipline, and a single living generative artwork at the heart.

Audience calibration: this site will be seen by A&Rs, by partygoers, and by brand teams at companies like Porsche, Ferrari, Cartier. Every design decision should survive the question "would a creative director at a luxury house respect this?"

## 2. The heritage rule (important, subtle)

The founder's Persian heritage informs the site's **geometry only** — never its copy, colors-as-cliché, or ornament. Concretely:
- The generative hero and all decorative line-work derive from **8-fold and 12-fold rosette symmetry and girih-style line systems** — pure mathematics, rendered abstractly.
- NO Farsi lettering, no tile textures, no arabesque ornament, no "Persian" mentioned anywhere in site copy or code comments that ship.
- The result should read to any viewer as "refined generative geometry." Only the founder knows the source. If a design choice makes the reference obvious, dial it back.

## 3. Design tokens

Palette — light, editorial, built around the brand green:
- `--green: #B8F06E`  (THE brand color. Permanent. Used as the house signature: solid fills behind key CTAs and graphic blocks, the oversized pillar numerals, the live dot, selection/highlight states, and traces inside the generative piece. Chartreuse is a SURFACE and GRAPHIC color here, not a text color.)
- `--green-ink: #24350D`  (deep green ink — for accent-colored TEXT: hover states, active links, small labels. Legible on paper where chartreuse text would not be.)
- `--paper: #F5F3EC`  (warm ivory — the site's ground. Chosen warm so the green pops against it without vibrating.)
- `--paper-deep: #ECE9DF` (section alternation)
- `--ink: #141310`  (near-black warm ink — all primary text; also the text color ON chartreuse fills, which is a high-contrast, signature pairing: ink-on-green.)
- `--ink-soft: rgba(20,19,16,.55)` (secondary text)
- `--hairline: rgba(20,19,16,.14)`
- Optional dark inversion: individual experience pages (Melomania, Manifold) MAY invert to `--ink` ground with `--paper` text and green accents for atmosphere — but the corporate/house pages stay light. This is where the green may glow; on light pages it never glows.

Discipline rule: the green is loud, so ration it — roughly one confident green moment per viewport (a filled CTA, a numeral, a highlight). Rarity on ivory is what makes it read as a house signature instead of a genre costume. Text selection (`::selection`) = green fill, ink text — a nice everywhere-signature.

Typography:
- Display: a sharp editorial serif with high contrast — first choice **"Editorial New"-class**; from Google Fonts use **Fraunces** (opsz axis, tight tracking at display sizes) or **Instrument Serif**. Display set HUGE: pillar names and the wordmark at clamp(4rem → 12rem). Tight leading (0.9), slight negative tracking.
- Text/UI: a clean grotesk — **Inter** or **General Sans**-class. Small caps/uppercase labels at wide tracking (.2em+) for eyebrows and metadata.
- NO monospace anywhere on the house site. NO Bebas Neue.
- Numerals: pillar numbering rendered oversized in the serif ("01", "02"…) as graphic objects, pop-art scale.
- `text-wrap: balance` on all headings and paragraphs. No orphaned words on a line.

### The house cut (proprietary type instance)

Fraunces is loaded as a full variable font (axes: `ital, opsz, wght, SOFT, WONK`). One locked instance — **the house cut** — applies everywhere the display serif appears (wordmark, pillar numerals & names, Labs typography, section headings). This is what makes the site's voice distinctive from a raw Fraunces setting.

Axis values (do not vary — ship via `var(--house-serif-mods)` in CSS):
- `SOFT: 40` — moderate warmth in terminals; still crisp
- `WONK: 0` — restraint over Fraunces's default swash-y idiosyncrasy (cleaner)
- `opsz`: sized per usage (24 for tagline / small display, 144 for hero display)
- `wght`: 300 by default (light editorial)

CSS pattern:
```css
:root {
  --house-serif: 'Fraunces', 'Instrument Serif', Georgia, serif;
  --house-serif-mods: 'SOFT' 40, 'WONK' 0;
}
.pillar-name {
  font-family: var(--house-serif);
  font-variation-settings: 'opsz' 144, 'wght' 300, var(--house-serif-mods);
}
```

The wordmark ships as **plain HTML text** in the house cut. Two block spans, one per line ("Melomaniac" roman + "Studios" italic), sized via `font-size: clamp(2.5rem, 13vw, 12rem)` so the type scales fluidly with the viewport without ever distorting glyph proportions. SVG rendering was tried and rejected: raster-fit `viewBox` sizing clipped at wide viewports, and `textLength / lengthAdjust="spacingAndGlyphs"` (the fix) visibly stretched Fraunces's glyphs. HTML text can neither clip nor distort — glyphs render at Fraunces's natural drawing.

**Follow-up (design pass, logged so it doesn't rot):** the fully-bespoke logotype needs a font-to-paths pipeline (offline design work — fontkit / opentype.js at build time, or a manual Illustrator/Figma pass). At that pass, flatten the house-cut Fraunces glyphs to outline paths and hand-modify 2–3 letterforms so the mark is truly proprietary — candidate surgery: a distinctive cut on the M's apex, a custom St relationship, a cropped terminal on the final s. Approximating this in the browser with stroked SVG paths (e.g. a "signature hairline") reads as a stray border cutting through the type, not as bespoke — so we don't ship that placeholder.

Layout & texture:
- Generous margins; a visible but quiet grid. Hairline rules (1px, `--hairline`) as the only borders — no rounded cards, no glows, no drop shadows except on the 3D render itself.
- Whitespace is the main material. When in doubt, remove.
- Buttons: rectangular or barely-rounded (2px), ink outline on paper; primary CTA = solid accent fill. Uppercase grotesk labels, wide tracking.

Motion:
- Slow, confident, expensive. Ease-out curves ≥ 600ms for section reveals; subtle parallax at most. NO bouncy easing, no fast flicker.
- Respect `prefers-reduced-motion`: static poster frame of the hero, no scroll animation.

## 4. The generative hero (the centerpiece)

Full-viewport opening scene on `--paper`. A single evolving 3D "sound-form": a sculptural object built from the rosette/girih line mathematics (Section 2), rendered in Three.js as thin ink-dark line systems and fine particle threads with occasional accent-orange traces — like a living etching, or a machine drawing an impossible instrument. It should feel gallery-grade, not screensaver.

Requirements:
- **Never the same twice**: seed the form's parameters (fold count 8/12/16, radii ratios, rotation drift, line density, where accent appears) from date+time on load. Constant slow evolution — visible change over ~20–30 seconds of watching.
- Palette-locked: ink lines on paper, with brand-green (`--green`) traces woven through — the green threads should feel like the living current inside an ink drawing, maybe 10–15% of the line work. No other hues, no neon glow, no gradients-as-decoration.
- Light interactivity: cursor/touch gently perturbs rotation or field. Optional (nice-to-have): a small "⊙ listen" toggle that lets the form react to mic or to a bundled ambient loop — off by default, muted styling.
- Overlaid content: tiny uppercase eyebrow ("MELOMANIAC STUDIOS"), the wordmark in the display serif, one line: "A house for sound." (placeholder — founder may rewrite), and a quiet scroll cue. Nothing else. No product pitch in the hero. No city mentions anywhere in house-level copy (eyebrow, footer, meta).
- Performance: cap pixel ratio at 2, degrade line density on mobile, lazy-init below-fold WebGL (Manifold page keeps its own separate scene).

## 5. Site architecture

Single-page hash routing may remain (keep existing router), but restructure content:

1. **HERO** — generative piece + wordmark (Section 4).
2. **THE HOUSE** — the four pillars as equals. A full-width editorial index: oversized serif numerals + names, one-line descriptions, hairline separators. Feels like a maison's list of métiers. Architected so adding pillar 05 is trivial (data-driven array → rendered rows).
   - 01 Record Label — "Independent. Artist-first." → existing artist page
   - 02 Sound Design — "Sonic identity for brands and spaces, zero to one hundred." → sound design page (this page should quietly flex ambition: selected clients, capabilities: sonic identity, live experiences, spatial sound, fashion shows)
   - 03 Melomania — "Original music, composed per night. Invite only." → melomania page (this page may use the dark inversion)
   - 04 Labs — "The experimental wing." → labs section/page
3. **LABS, FEATURED** — directly after the house index, a spotlight: "The newest wing of the house." Grid of experiments: Manifold, Frequency, Noted — numbered 01/02/03 within Labs, each with name (display serif), one-liner, Launch link. Room for more. Labs is the playful wing and may carry more green than the rest of the site — e.g., a full chartreuse band/section with ink text, the boldest pop-art moment on the page — while everything around it stays quiet paper.
4. **Pillar pages** — restyled to the new system (keep existing content/functionality: artist links, Frequency quiz, Melomania event card, sound design clients).
5. **Early Access** — restyled; copy updated to the house voice.
6. **Footer** — wordmark, contact, social. Quiet. No city mention.

Functionality to preserve exactly: hash router, Supabase auth + waitlist + Frequency quiz logic, Manifold WebGL app (Manifold's own page may keep a dark ground for the visualizer; update its UI chrome — buttons, labels — to the new type/accent system, dropping chartreuse).

## 6. Explicit anti-patterns (hard NOs)

- No black-background house pages (dark inversion allowed ONLY on Melomania/Manifold experience pages). No neon glow treatments of the green on light pages — flat, confident, print-like.
- No second accent color, ever. The green is alone.
- No monospace, no Bebas Neue, no terminal/console aesthetics, no scanlines, no glitch effects.
- No generic "underground electronic" tropes. If a component would look at home on a techno label's Linktree, redesign it.
- No overt cultural signposting per Section 2.
- No more than one accent moment per viewport. No emoji in UI.

## 7. Deliverables & workflow

- Rebuild as `head.html` + `body.html` (Webflow custom-code embeds, same integration as today) AND as a standalone `index.html` for local preview and possible future GitHub Pages migration.
- Serve locally with live reload for review; founder approves section by section (hero first — get the generative piece feeling right before styling anything else).
- Keep a `CHANGELOG.md`. Commit per section.
- Follow-up task once approved (separate pass): bring NOTED (noted.melomaniacstudios.com) into this system — it keeps its green and (being a night-ride tool) may keep a dark ground as an "experience page," but adopts the new typography and drops Bebas/DM Mono. Interaction model untouched.

---

## 8. Type experiment — Melomaniac Serif v0.1 (branch `type-experiment`)

The house cut (§ 3) got us to a distinct instance of Fraunces. This step is the next move on the founder's roadmap: a display face that reads as *ours* to a human eye — like the NYT masthead reads as the New York Times' own — without waiting on a fully commissioned typeface. Two-part experiment on `type-experiment` (not on main until approved via `/type-review.html`).

### 8a. Bolder display weight (safe, immediate)

`--house-serif-display-wght: 600` in `:root`. Every declaration that draws Fraunces at `opsz 144` (wordmark, pillar-page titles, section headlines, Labs product titles, modal titles, quiz titles, footer wordmark — 16 sites) references the variable via `font-variation-settings: 'opsz' 144, 'wght' var(--house-serif-display-wght), var(--house-serif-mods)`. Small `opsz 24` marks (italic leads, mini arrows, back-chevrons) intentionally stay at wght 300.

This one CSS variable is the whole knob for the "voice" of display type across the site. If wght 600 reads too heavy in production, one edit to `:root` retunes everything.

### 8b. Melomaniac Serif v0.1 (bespoke, gated behind review)

A modified derivative of a high-contrast SIL-OFL display serif, exported as our own family. Deliberately v0.1 — a first pass we can iterate on; not a substitute for the commissioned face still on the roadmap.

**Base font.** Bodoni Moda (SIL Open Font License 1.1, © 2020 The Bodoni Moda Project Authors — https://github.com/indestructible-type/Bodoni). Fetched as a variable font (opsz + wght axes) from Google Fonts' `ofl/bodonimoda/` directory. Chosen after evaluating candidates: rejected Fraunces (already the house serif) and Playfair (too common as a "premium display" default); Bodoni Moda wins on high vertical contrast, an actively-maintained OFL project, both wght + opsz axes on the variable, and shipping italics we can grow into. The OFL license is bundled at [fonts/Bodoni-Moda-OFL.txt](fonts/Bodoni-Moda-OFL.txt). Bodoni Moda reserves no Reserved Font Name (RFN), so v0.1 can legally take the new family name "Melomaniac Serif" as long as the OFL notice ships alongside.

**Modification recipe (v0.1).** Applied by [type-experiment/build_melomaniac_serif.py](../scratchpad/type/build_melomaniac_serif.py) — a fontTools-based build script kept out of the main repo (it needs the base .ttf and a Python 3 env). Every rule is systematic and documented so a future rebuild is repeatable.

| # | Rule | What it does | Delta |
|---|---|---|---|
| R1 | Identity rename | Rewrites the `name` table so browsers and OSes see this as its own family. Family = "Melomaniac Serif", version 0.100, unique ID and manufacturer credit Bodoni Moda upstream. | All `name` records (nameIDs 1, 2, 3, 4, 5, 6, 8, 10, 16, 17, 21, 22) rewritten on both Windows Unicode English and Mac Roman platforms. |
| R2 | Subset | Reduces the font to the 105 codepoints the site actually uses: ASCII printable plus em-dash, en-dash, ellipsis, middle-dot, curly quotes (‘’“”), and Unicode arrows (→ ←). Layout features preserved. | 162 KB base TTF → 84 KB modified TTF → 50 KB WOFF. |
| R3 | Tightened advances | Reduces each glyph's advance width by 3% and centre-shifts its outline by half of the delta so the glyph stays optically centred in the smaller advance box. Reads as tighter aperture at display sizes. | Applied to 544 glyphs. |
| R4 | Apex clip on M / N | Drops the topmost 1.5% point band by 12 font units, creating a subtle cut/flattened apex distinct from Bodoni Moda's default pointed apex. | 2 glyphs, 12-unit y-drop on apex ridge. |
| R5 | Spine kern on S | Shifts the four mid-height inflection points (middle third by y, middle half by x) horizontally by +18 units, steepening the perceived spine angle. | 1 glyph, +18-unit x-shift on 4 spine points. |
| R6 | Ear/spur trim on a / e | Translates the top-right terminal point band (top 15% by y, right 25% by x) inward — for `a` by (-8, -6) font units (the ear), for `e` by (-6, -4) (the spur). | 2 glyphs, 1 point each. |

**How the file gets built.** Prereqs: Python 3.10+ and `fontTools` in the environment. WOFF2 output requires `brotli`; v0.1 ships only WOFF (~50 KB) because the initial build environment couldn't install brotli — the script writes WOFF2 automatically when brotli is present.

```bash
# From the scratchpad/type/ directory (script and BodoniModa.ttf both live there)
python3 build_melomaniac_serif.py
# Writes MelomaniacSerif-v0.1.{ttf,woff,woff2?} in-place.
# Copy the WOFF (and the OFL text) into the site's fonts/ dir:
cp MelomaniacSerif-v0.1.woff <repo>/fonts/melomaniac-serif-v0.1.woff
cp MelomaniacSerif-v0.1.ttf  <repo>/fonts/melomaniac-serif-v0.1.ttf
cp BodoniModa-OFL.txt         <repo>/fonts/Bodoni-Moda-OFL.txt
```

**Where it plugs in.** A `@font-face` declaration in `type-review.html` (and, on merge, the main stylesheet) points at `./fonts/melomaniac-serif-v0.1.woff` with the `.ttf` as a legacy fallback. On merge, the `--house-serif` custom property would prepend `'Melomaniac Serif'` ahead of Fraunces so it wins for display text; body/UI keeps `'Inter', sans-serif` untouched. The Fraunces load stays because it's still the source of the italic wordmark line ("Studios") — v0.1 is roman-only.

### 8c. Review gate

[type-review.html](type-review.html) renders a strict two-column comparison at the same weight, opsz, and content: wordmark, "The House", the four pillar names, a MELOMANIA pillar-title, and a three-size scale test. Column A is Fraunces at wght 600 (what 8a alone would ship); Column B is Melomaniac Serif v0.1. Merge decision comes from that page — no shipping v0.1 to main without founder approval on the review page.

### 8d. What v0.1 explicitly is NOT

- Not a commissioned face. Still on the long-term roadmap and still the right eventual answer for the wordmark (§ 3 already flagged this).
- Not a full family. Roman-only. Italic ("Studios" in the wordmark) continues to render in Fraunces italic — that stays until an italic companion is drawn.
- Not exhaustive in its glyph edits. R4/R5/R6 use programmatic heuristics (topmost point band, middle-third + middle-half, etc.) rather than the point-by-point work a font designer would do in Glyphs/RoboFont. Where the heuristics don't land, that's a v0.2 target — the recipe is trivial to iterate on.
- Not a substitute for the discipline in § 3. The house cut applies as before to whichever face wins the review; MELOMANIA still gets its +.01em caps tracking; small-opsz italic leads still ride Fraunces at wght 300.

