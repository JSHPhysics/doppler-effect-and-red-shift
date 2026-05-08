# Project: Doppler Effect & Cosmological Redshift Simulation

Build a single, self-contained HTML file (`index.html`) — vanilla JS, inline CSS, no build step, no external dependencies except optionally MathJax via CDN for equation rendering. Target: A-level Physics (Edexcel 9PH0, Topic 13). Author attribution in footer: **Joshua Stafford-Haworth (JSHPhysics)**.

## Design principles

- Clean, minimalist, light theme. Plenty of whitespace. Sans-serif (system stack).
- Low cognitive load: one focal canvas at a time, controls grouped logically, readouts in a fixed side panel.
- Touch-friendly: works on tablet, laptop, classroom whiteboard. Buttons ≥44px, sliders chunky.
- Responsive down to ~1024px wide; degrades gracefully on phones (controls below canvas instead of beside).
- No animation chrome — no shadows, gradients, or decoration unless they encode physics.
- Colour palette restricted: greys/whites for UI, a red↔blue physics gradient for shift, one accent (e.g. amber) for highlights.

## Layout

A top tab bar with two modes:
1. **Classical Doppler**
2. **Cosmological Redshift**

A small persistent header explains the current mode in one line. A footer carries the attribution and a "?" button opening a brief pedagogy panel (the key distinction between the two — see below).

## Mode A: Classical Doppler

### Canvas
- Centre-left: a 2D canvas (~700×400) with a moving source (small filled circle) emitting circular wavefronts at fixed time intervals from its position at time of emission.
- Wavefronts persist and expand at speed `c` (use a normalised wave speed; it's not relativistic, just a visual `c`).
- Stationary observer (eye/ear icon) on the right side of the canvas.
- Each wavefront is tinted along a red↔blue gradient according to its **local apparent wavelength** at the observer's azimuth — i.e. the wavefronts in front of the source appear bluer, behind appear redder. Use a perceptually reasonable gradient (e.g. blue #2b6cb0 → grey #888 → red #c53030).
- Source motion is left-to-right by default; sign of velocity flips direction.

### Controls (right panel)
- **Source velocity** slider: −0.9c to +0.9c (positive = toward observer). Display value as fraction of c.
- **Source frequency** slider: arbitrary units, e.g. 1–10 Hz (visual rate of wavefront emission).
- **Play / Pause / Reset** buttons.
- Toggle: "Show wavefront emission points" (small dots marking where each wavefront was emitted — this is the standard textbook diagram).

### Live readouts
- Emitted wavelength λ₀
- Observed wavelength λ' (at the observer's location/direction)
- Δλ = λ' − λ₀
- z = Δλ/λ₀
- Observed frequency f'
- Use the non-relativistic formula: `λ' = λ₀ (1 + v_r/c)` where `v_r` is radial recession velocity from observer. State the formula visibly in the panel.

### Spectrum line strip
- A horizontal strip below the canvas showing a reference absorption spectrum (a few vertical black lines on a rainbow gradient, like Hα, Hβ, Na D).
- The strip shifts horizontally according to z. Label "Lab frame" vs "Observed frame" with both shown so students see the shift directly.

## Mode B: Cosmological Redshift

### Canvas
- Horizontal 1D representation of space: a chain of ~7 galaxies (small spiral icons or just labelled dots G₁…G₇) sitting on a coordinate grid of vertical tick lines.
- The grid itself stretches over time — the **comoving coordinates** of galaxies stay fixed, but the physical separation between tick lines grows as scale factor `a(t)` increases. This makes it visually obvious that galaxies aren't moving *through* space; space itself is expanding.
- A photon launched from a chosen galaxy (default G₁, far left) toward an observer at G₇ (far right). The photon is drawn as a short sinusoidal wave packet (~3 wavelengths visible).
- As the photon travels, its wavelength visibly stretches in proportion to a(t). Tint shifts from blue → red as it stretches.

### Controls
- **Hubble constant H₀**: slider, e.g. 50–100 km/s/Mpc (exaggerated visually so expansion is observable in seconds, but display the real-units value).
- **Choose emitting galaxy**: G₁…G₆ (G₇ is observer). Distance to observer auto-derived.
- **Launch photon** button. **Play / Pause / Reset**.
- Toggle: "Freeze grid" — pauses expansion to compare at an instant.

### Live readouts
- Scale factor a(t), with a(now) = 1
- Distance d (Mpc) of emitting galaxy
- Recession velocity v = H₀d
- z = (a_obs / a_emit) − 1 — show this is the **stretching ratio**, not a velocity-based formula.
- Approximate age of universe = 1/H₀ (in Gyr), for context.
- Display the formula `1 + z = a(t_obs)/a(t_emit)` prominently. Contrast it with the Doppler formula from Mode A in a small "compare" tooltip.

### Hubble's law plot
- Small inset plot (right side or below canvas): v on y-axis, d on x-axis.
- Each galaxy plotted as a point; a straight line v = H₀d through the origin.
- Updates live as H₀ changes. When the user launches a photon from a galaxy, that galaxy's point pulses briefly.

### Spectrum line strip
- Same component as Mode A, but shift driven by z from the cosmological formula.

## Pedagogy panel ("?" button)

Two short paragraphs — student-readable, ~80 words each:

1. **Doppler shift** — caused by a source moving *through* space relative to the observer. The wavelength change happens *at emission*. Use the formula z = v/c (for v ≪ c). Example: nearby star moving in our galaxy.

2. **Cosmological redshift** — the photon's wavelength stretches *during its journey* because space itself expands. Galaxies aren't really moving through space; their comoving coordinates are fixed. Use 1 + z = a_obs/a_emit. This is the redshift seen for distant galaxies and is the basis of Hubble's law and evidence for an expanding universe.

End with one line: *"For nearby galaxies the two formulas give the same answer — that's why z = v/c is taught first. For distant galaxies, only the cosmological formula is correct."*

## Tablet & touch implementation

Primary use environment is iPad and classroom whiteboard. Build mobile-first; do not retrofit.

### Viewport and layout
- `<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">` — allow pinch zoom for accessibility, do not lock scale.
- Use `100dvh` (with `100vh` fallback) for full-height layout to avoid iOS Safari URL-bar resize bugs.
- Respect safe area insets on the outer container: `padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left)`.
- Must work in both iPad orientations. Landscape is primary classroom orientation; verify portrait too.
- Add iOS web-app meta tags so it can be saved to home screen for classroom use:
  - `<meta name="apple-mobile-web-app-capable" content="yes">`
  - `<meta name="apple-mobile-web-app-status-bar-style" content="default">`
  - `<meta name="apple-mobile-web-app-title" content="Doppler & Redshift">`

### Touch targets
- All interactive elements ≥44×44 CSS px (Apple HIG). Slider thumbs ≥28px visible, with a 44px hit area via padding or a transparent overlay.
- Minimum 8px spacing between adjacent touch targets.
- Clear `:active` pressed state on buttons — do not rely on `:hover`, which is unreliable on touch.

### Touch interaction
- Use **Pointer Events** (`pointerdown` / `pointermove` / `pointerup`) — one code path covers mouse, touch, and Apple Pencil.
- Set `touch-action: manipulation` on interactive elements to remove the 300ms tap delay and prevent double-tap zoom on controls.
- Set `touch-action: none` on canvases that capture gestures, and `overscroll-behavior: none` on `body` to prevent pull-to-refresh interfering when dragging on the canvas.
- Disable iOS UI artefacts on controls:
  - `-webkit-touch-callout: none`
  - `-webkit-tap-highlight-color: transparent`
  - `-webkit-user-select: none` on buttons and labels (do **not** apply to readout values — students may want to copy a z value).
- Sliders: native `<input type="range">` with custom CSS. Do not reinvent. Verify the thumb is comfortable to grab on iPad.

### Canvas rendering
- Handle `devicePixelRatio`: backing-store size = CSS size × DPR, then `ctx.scale(dpr, dpr)`. Otherwise canvases blur on Retina iPads.
- Re-handle DPR and resize on `resize` and `orientationchange`.
- Listen for `visibilitychange` and pause/resume the animation loop cleanly — iPad Safari throttles background tabs aggressively.

### Visibility and clarity (classroom + projected whiteboard)
- Body text ≥16px; readout labels ≥18px; primary readout numbers (z, λ, v) 24–28px, so they read from 2–3m away on a projected board.
- Contrast ≥4.5:1 against background (WCAG AA). Avoid grey-on-white below ~#666.
- **Do not encode information in colour alone.** The red↔blue shift gradient must always be paired with a numeric z value and a directional label ("approaching" / "receding" or "redshift" / "blueshift").
- Visible `:focus-visible` ring for keyboard / Switch Control / external-keyboard iPad use.
- Respect `prefers-reduced-motion`: when set, disable UI flourishes (transitions, pulse highlights). Keep the physics animation — that is the content.
- No flashing faster than 3Hz anywhere (PEAT / classroom safety).

### Performance
- Target a steady 60fps on iPad (9th gen) and above. If frame budget is tight, reduce wavefront count rather than dropping frame rate.
- Rate-limit DOM readout updates to ~10Hz via `textContent` — do not update text nodes every frame.

## Code organisation

- One file: `index.html`. CSS in `<style>`, JS in `<script>` at end of body.
- JS structured as two modules (objects) `dopplerSim` and `cosmoSim`, each with `init()`, `update(dt)`, `render()`, `destroy()`. Tab switch destroys the inactive one cleanly to avoid background animation.
- Use `requestAnimationFrame` with delta-time stepping so behaviour is consistent across refresh rates.
- All physics constants and tunable visual parameters at the top of the script in a clearly commented `CONFIG` block — easy for me to tweak in class.
- No external libraries unless strictly necessary. If MathJax is used for the formula rendering, load from CDN with `defer`.

## Acceptance criteria

- [ ] Loads with no console errors or warnings.
- [ ] Both modes run smoothly at 60fps on a mid-range laptop.
- [ ] Physics readouts match formulas to 3 sig fig.
- [ ] In Mode A at v = 0, observed = emitted (no shift). At v = +0.5c, z ≈ −0.5 (blueshift). At v = −0.5c, z ≈ +0.5.
- [ ] In Mode B, doubling H₀ doubles all recession velocities; v–d plot remains a straight line through origin.
- [ ] Photon wavelength in Mode B is visibly larger on arrival than at emission for any non-zero H₀.
- [ ] Spectrum strip shifts in the correct direction in both modes.
- [ ] Layout is usable on a 1024-wide screen and on a tablet in landscape.
- [ ] All controls keyboard-accessible; no flashing/strobing animations.
- [ ] Tested on iPad Safari in both landscape and portrait — no horizontal scroll, no overlap, all controls reachable.
- [ ] All touch targets verified ≥44×44 CSS px.
- [ ] Canvases render crisp on Retina (devicePixelRatio handled and re-applied on resize/orientation change).
- [ ] No 300ms tap delay; sliders track the finger immediately on drag.
- [ ] Pinch-to-zoom is **not** blocked (accessibility requirement).
- [ ] Dragging on the canvas does not trigger pull-to-refresh or page scroll.
- [ ] Primary readouts legible from ~2m away (font-size sanity check on a projected display if possible).
- [ ] Every piece of information conveyed by colour also has a text or numeric equivalent.
- [ ] `prefers-reduced-motion` honoured for UI flourishes; physics animation continues.
- [ ] Animation pauses cleanly when the tab is hidden and resumes on return.
