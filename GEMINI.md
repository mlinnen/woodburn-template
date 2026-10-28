# Woodburn Template Generator — Project Guidelines

## Architecture & Source of Truth
- **Single-File App:** `src/template_printer.html` is the sole application source file. Keep HTML, CSS, and vanilla JavaScript self-contained in this single file using `jsPDF` via CDN so it can always be opened directly from disk (`file://`) without a local server or bundler.
- **Samples:** `samples/` holds example input artwork and generated PDF output referenced by `README.md`.

## Core Domain Invariants
1. **True 1:1 Physical Scaling:**
   - All `jsPDF` coordinates and page sizes are in inches (`unit: 'in'`) on standard US Letter (`11.0" × 8.5"` landscape or `8.5" × 11.0"` portrait).
   - Target Width and Target Height inputs are bidirectionally locked to the uploaded image's aspect ratio (`activeDimension` tracks which input the user edited last).
2. **Minimum Seam Margin & Overlap (`MIN_MARGIN = 0.75"`):**
   - Each page has a maximum active tile area of `(pageW - 1.5") × (pageH - 1.5")` (`9.5" × 7.0"` in landscape, `7.0" × 9.5"` in portrait).
   - Each tile `(c, r)` is centered on its page (`tileLeftX = (pageW - tileW) / 2`, `tileTopY = (pageH - tileH) / 2`) so adjacent artwork naturally extends into the margins past the red dashed cut/alignment lines on all internal boundaries.
3. **Smart Orientation (`Auto` / `Landscape` / `Portrait`):**
   - In `auto` mode, compare total pages required for Landscape vs. Portrait and choose whichever uses fewer pages (breaking ties using `targetH > targetW ? 'portrait' : 'landscape'`).
4. **Clean Export vs. Preview Canvas:**
   - Always render the clean (normal or horizontally mirrored) image onto an offscreen canvas (`renderCleanImageToCanvas`) when generating the JPEG for `jsPDF`. Never bake the preview canvas's red dashed split lines into the PDF image payload.

## Deployment & GitHub Pages
- **Workflow:** `.github/workflows/deploy.yml` builds the static site on push to `main` by copying `src/template_printer.html` to `dist/index.html` and `dist/app/index.html`, then deploys `dist/` to GitHub Pages.
- **Public URLs:**
  - `https://mlinnen.github.io/woodburn-template/app`
  - `https://mlinnen.github.io/woodburn-template/`
- Do not commit `dist/` to version control (it is ignored via `.gitignore`).
