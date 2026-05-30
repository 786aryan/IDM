# U.S. Drought Monitor — Clone

A faithful, working clone of the U.S. Drought Monitor (droughtmonitor.unl.edu),
built from the supplied HTML mirror and walkthrough video.

## How to view

Just open `index.html` in a browser. Everything works from disk — no server required.
All cross-page links use relative paths.

For the cleanest experience, you can also serve the folder over a local server:

```bash
cd usdm_clone_final
python3 -m http.server 8000
# then open http://localhost:8000
```

## Pages (15 total)

- **index.html** — Current Map (home). Real May 19, 2026 USDM map with grayscale toggle,
  authors block, data cutoff alert, full D0–D4 legend with impact key, summary excerpt
- **maps.html** — Maps landing (7 product cards)
- **compare.html** — Compare Two Weeks side-by-side with stat bars
- **slider.html** — Comparison Slider — drag the red handle to wipe between two weeks
- **animations.html** — Multi-week animation player (Play/Pause/Prev/Next + scrubber)
- **archive.html** — Map Archive thumbnails
- **data.html** — Data landing (9 data product cards)
- **data-download.html** — Comprehensive Statistics form + preview table
- **data-tables.html** — State-by-state breakdown table
- **data-graphs.html** — Stacked-area time-series SVG chart (2000–2026)
- **summary.html** — National Drought Summary — full text for all 8 regions
- **about.html** — What is the USDM?
- **conditions.html** — Conditions & Outlooks landing (8 product cards)
- **contact.html** — Contact authors and NDMC

## Interactive features (all client-side, no JS dependencies)

- Map grayscale toggle (home page)
- Dropdown menus (Regions)
- Comparison slider with mouse + touch drag
- Animation player with looping playback, scrubbing, step controls
- All form controls (selects, date pickers, search fields)

## Files

```
usdm_clone_final/
├── index.html
├── maps.html, data.html, summary.html, about.html, ...
└── assets/
    ├── styles.css      (shared design system)
    ├── app.js          (interactive features)
    ├── map_20260519.png, map_20260512.png, map_20260505.png, map_20260428.png
    └── usda.svg, ndmc.svg, doc.svg, noaa.svg, nasa.svg
```

## Design notes

- **Typography**: Lora (display) + Source Sans 3 (body) loaded from Google Fonts
- **Color palette**: Official USDM red (#da3910) for nav, official D0–D4 intensity colors for the legend and charts
- **Layout**: CSS Grid throughout, 1280px max page width, fully responsive down to mobile
- **No frameworks**: vanilla HTML/CSS/JS — no Bootstrap, no React, no build step

## Data

The drought maps are the real USDM PNGs from your mirror (May 19, 12, 5, and April 28,
2026). The regional summary narrative on `summary.html` is the actual text from the
mirror's Summary page. The numeric values in `data-download.html`, `data-tables.html`,
and `data-graphs.html` are illustrative placeholders for the clone demo.
