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

### Pending review
- Serving locally at http://127.0.0.1:4200 — awaiting founder approval on the
  moving piece before styling any other section (per DESIGN.md §7 workflow).

### Next
- The House editorial index (four pillars as equals).
- Labs featured spotlight (allowed the boldest chartreuse band on the page).
- Pillar pages restyled; Melomania dark inversion allowed.
- Early Access restyled; port waitlist + Frequency + Manifold logic from `_reference/`.
- GitHub Pages + custom domain once the full site is approved.
