# India Drought Monitor — Change Summary (v5)

Five UI/feature additions on top of the WCL-colour-mapped live build.

## 1. State name in the side panel

A new "State" card sits at the top of the right-hand rail (above the Cursor Readout) on
the Current, Conditions and Archive map pages. It shows the name of the state under the
cursor, updating live as you move over the map (engine already tracks
`state.hoveredStateName`; `wireReadout` now also fills `#ro-state-big`).

## 2. Greyscale option

A "Greyscale" checkbox renders the map in perceptual-luminance grey. It is implemented in
the engine's colour function (`getOfficialCDIColor` converts the WCL colour to grey when
`state.grayscale` is on), so the on-screen map AND any exported PNG/GIF are greyscale.
Exposed via `setGrayscale()` / `getGrayscale()`. Added to Current, Conditions, Archive and
Animations pages.

## 3. Union Territories removed from dropdowns

The "Jump to State" dropdowns now list states only. A `UT_IDS` set in
`drought-map.app.js` (Andaman & Nicobar, Chandigarh, Dadara & Nagar Havelli, Daman & Diu,
Jammu & Kashmir, Lakshadweep, NCT of Delhi, Puducherry) is filtered out in
`wireStateSelect`. Placeholder and readout labels updated from "State / UT" to "State".
(The map itself and the data-tables page still include all regions; only the dropdowns
changed, per request.)

## 4. Interpolation slider on the Animations page

The "Detail" interpolation slider (driving Pranav's `INTERP`) is now also present in the
Animations control panel, alongside the date range and frame-rate inputs.

## 5. Download map (PNG) and animation (GIF)

- **PNG** — a "Download PNG" button on the Current, Conditions, Archive and Animations
  pages flattens the data layer onto a white background and downloads a clean
  `india-drought-*.png` (no HUD overlay). New engine methods `toPNGDataURL()` /
  `captureRasterCanvas()`.
- **GIF (animations only)** — a "GIF" button on the Animations page iterates the chosen
  week range, renders each frame, captures the flattened raster, and encodes an animated
  `india-drought-animation.gif` at the chosen frame rate, with a progress indicator.
  Uses gif.js (vendored locally at `assets/vendor/gif.js` + `gif.worker.js`).

## Files touched

- `drought-map.engine.js` — grayscale in colour function + `state.grayscale`/`opts.grayscale`;
  API: `setGrayscale`/`getGrayscale`, `toPNGDataURL`, `captureRasterCanvas`.
- `drought-map.app.js` — UT filter in `wireStateSelect`; `stateBig` readout; greyscale,
  PNG-download and (existing) interp-slider wiring in `mountMap`.
- `drought-map.css` — state-name card, GIF status styles.
- `index.html`, `conditions.html`, `archive.html` — greyscale checkbox, Download PNG,
  state-name card, label fixes, config wiring.
- `animations.html` — interp slider, greyscale, PNG + GIF buttons, GIF builder; loads gif.js.
- New: `assets/vendor/gif.js`, `assets/vendor/gif.worker.js`.

## Validation

All 14 pages load with intact chrome and zero JS errors. Verified headless: state name
shows on hover; greyscale toggles the rendered map (top colour 255,255,0 -> 226,226,226);
UTs absent from dropdowns (29 -> states only); interp slider present on Animations; a real
275 KB GIF and a PNG both download. Serve over HTTP; runs on GitHub Pages.
