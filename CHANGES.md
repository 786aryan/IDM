# India Drought Monitor — Change Summary (v3)

Builds on the live, no-PNG rebuild. This revision adds the three requested items.

## 1. Exact WCL colour-maps (from github.com/wcl-iitgn/IDM)

The old production site rendered pre-baked raster images, so its `Legend*.jsx`
components ARE the authoritative value->colour spec. All four were transcribed into:

- `assets/interactive/drought-map.colormaps.js`
  - **CDI / drought** (Legend.jsx): brown / red / orange / rgb(252,214,148) / yellow /
    white at -2.0 / -1.6 / -1.3 / -0.8 / -0.5 (white = normal above -0.5).
  - **SPI / SRI / SSMI** (Legend4.jsx): 12-step diverging scale, -3.0 ... +3.0.
  - **Streamflow** (Legend3.jsx): 12-step percentile scale, 0 ... 100.
  - **Drought persistence** (Legend2.jsx): recover (#99CCFF) / persist (#FF9999).
  - CSS named colours resolved to exact hex (brown=#A52A2A, etc.).

The engine's pixel colouring (`getOfficialCDIColor`) now keys off the **actual data
value** via the active colour-map, instead of a per-frame min/max normalisation. The
correct effect: most of India shows near-normal (white/yellow), with drought only where
values are genuinely low. The product switcher (Conditions page) swaps the colour-map to
match each product; the legend swatches and the hover-readout class label use the same
maps. `buildMap()` selects the right map automatically per product, so every page
(including Compare and Slider) renders with the exact WCL colours.

## 2. Rectangle-zoom toggle button

- Added `state.zoomMode` ('state' default | 'rect') to the engine, exposed via
  `setZoomMode()` / `getZoomMode()`.
- A dedicated toolbar button (styled like the Play button) toggles between
  **click-state-zoom** and **rectangle-zoom**; the cursor becomes a crosshair in rect
  mode. Added to the Current, Conditions and Archive map pages.
- Pranav's rect-zoom code is preserved, but the mousedown now only starts a selection
  box in rect mode (in state mode a click zooms straight to the state). While fixing this
  I also repaired a latent bug: the mousemove handler never updated the selection-box
  corner, so the rectangle had zero size and could never zoom — it now tracks the live
  (clamped) corner, and box-zoom works.

## 3. Comparison slider fixed (true overlay)

Previously the clipped (top) map was rescaled to the clip container's width while the
base map stayed full size. Now both canvases are identical full-size layers stacked
exactly on top of each other; the top (older) layer is revealed only left of the handle
via `clip-path: inset(0 calc(100% - var(--wipe)) 0 0)`, so it is genuinely overlaid on
the newer map and never resized. A single `--wipe` CSS variable on the stage drives both
the clip and the handle position. Verified: both maps display at identical size and the
wipe reveals the older map up to the handle.

## Files touched

- New: `assets/interactive/drought-map.colormaps.js`.
- Edited: `drought-map.engine.js` (colour-map hook, zoomMode, rect-zoom fix, API),
  `drought-map.app.js` (colour-map per product, zoom-mode button wiring, readout labels),
  `drought-map.css` (WCL legend swatches, zoom-button active state, slider clip-path),
  `index.html` / `conditions.html` / `archive.html` (zoom button + colormaps include),
  `slider.html` (clip-path wipe via --wipe), and all engine pages (colormaps include).

## Validation

All 14 pages load with intact chrome and zero JS errors; every map paints live with the
exact WCL colour-maps; the zoom toggle and the slider overlay both verified headless.
Serve over HTTP (maps fetch data files); runs on GitHub Pages as-is.
