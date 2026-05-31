# India Drought Monitor — Change Summary (v6: Hydrological Outlook)

This revision integrates the **India Hydrological Outlook (IHO)** outputs produced by the
`IHO_Pipeline_Final.ipynb` notebook, and adds a PDF report archive. The existing interactive
drought maps are unchanged — the IHO content is image/PDF-based, as the pipeline already renders it.

## New section: Hydrological Outlook (3 pages + nav entry)

A new top-level nav item **"Hydrological Outlook"** links a three-page section:

1. **hydro.html — Dashboards.** Shows the composite dashboard PNG for each variable, laid out
   exactly as the pipeline renders it (current month + four prior months on top; forecast,
   same-month-last-year, and the driest/wettest analogues on the bottom; percentile colourbar in
   the middle). A variable selector switches between the five variables; the dashboard can be
   opened full-size or downloaded as PNG. Mirrors the look of indiahydrolook.in.

2. **hydro-maps.html — Individual Maps.** The individual maps that make up each dashboard, shown
   separately at full size in a responsive grid (task: "view the individual maps … shown
   collectively in the dashboard"). The current-month and forecast panels are tagged; clicking any
   map opens it in a lightbox. Deep-links per variable via `#<Variable>` (the dashboards page links
   straight here for the selected variable).

3. **hydro-reports.html — PDF Reports.** The full PDF report archive (task: "the whole pdf archive
   should be visible to the user for downloading"). A dated list on the left; selecting one previews
   it inline in an `<iframe>` and exposes a direct **Download PDF** button. Each list row also has a
   one-click download icon. A graceful fallback link appears if a browser blocks inline PDFs.

### Variables included
Rainfall, Surface Air Temperature, Relative Wetness (soil moisture), Total Runoff, and
Evapotranspiration. **Streamflow at Gauge Stations** and **Streamflow at Stream Network** are
excluded per the data notes ("To Be Removed").

## How it's wired (data-driven, no build step)

- `assets/hydro/hydro-manifest.json` — lists the five variables, each with its dashboard file and
  its nine individual-map files (with display labels and current/forecast role tags). Generated
  from the actual files in `Hydrologic_Outlook/Output/All_Maps/`.
- `assets/hydro/reports-manifest.json` — the PDF archive, newest first, with dates parsed from the
  `Hydrolook_YYYY_MM_DD.pdf` filenames. Add a new PDF + one line here to publish the next month.
- `assets/hydro/hydro.js` — `IHO.initDashboards / initMaps / initReports`. Handles URL-encoding of
  the image filenames (which contain spaces and parentheses), the variable selector, the lightbox,
  and the PDF viewer.
- `assets/hydro/hydro.css` — styling, using the site's existing design tokens.

## Files / assets added

- `hydro.html`, `hydro-maps.html`, `hydro-reports.html`
- `assets/hydro/{hydro.js, hydro.css, hydro-manifest.json, reports-manifest.json}`
- `Hydrologic_Outlook/Output/Dashboards/*.png` (5 dashboards)
- `Hydrologic_Outlook/Output/All_Maps/<Variable>/*.png` (5 variables × 9 maps)
- `Hydrologic_Outlook/Output/PDF_Archive/Hydrolook_2026_02_28.pdf`

## Nav & footer

Nav is now 8 items (added "Hydrological Outlook"). The footer gains a "Hydrological Outlook" column
(Dashboards / Individual Maps / PDF Reports). Nav + footer were regenerated consistently across all
17 pages.

## Validation

All 17 pages load with intact chrome, a consistent 8-item nav, and **zero JS errors**. Verified
headless: dashboards switch per variable and load (3080-px PNGs); all nine individual maps load per
variable with current/forecast tags and a working lightbox; the PDF archive lists and previews the
report and the download links resolve (HTTP 200). The interactive drought maps from earlier versions
are untouched and still render.

> Note: like the rest of the site, serve over HTTP (the pages fetch JSON/PNG/PDF). On `file://`,
> inline PDF preview and the manifests won't load. Runs on GitHub Pages as-is.
