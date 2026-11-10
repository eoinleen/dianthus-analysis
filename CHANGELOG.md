# Changelog

Versions match `NOTEBOOK_VERSION` in the notebook and the Git tags.

## v33 — 2026-11 (first release in this repository)
- Two-site model (`FIT_MODEL = "two_site"`; `TWO_SITE_KD2_NM` to fix Kd2); unresolved two-site fits flagged and marked N.D.
- Diagnostics on every sample: ΔAICc vs 1:1, non-specific, Hill and two-site; Hill EC50 / n_H saved.
- Optional Hill figure set (`HILL_FIGURES`): panels, overlays, linearised Hill plot, own bootstrap CIs.
- Reads Dianthus software export tables directly (one table = one repeat); `DROP_ANOMALIES`, `MAX_CONC_NM`.
- Figure rows (D/E style) sized to `PAGE_WIDTH_MM`, identical data boxes, legend with K_D ± error, n and R².
- One set of font sizes for every output; LABELS settings block for all figure text.
- x-axis switches to 10^n from `X_SCI_FROM`.
- Methods text states excluded wells and the notebook version.
- **Fixed:** wide sheets without a column-header row lost their first data row (v32 and earlier, `DATA_START_ROW = 2`). Results from such files made with earlier versions used one fewer concentration.
- **Fixed:** `TOTAL_TARGET_NM` was frozen when the analysis cell ran; now re-read on every run.
- **Fixed:** fraction-bound axes clipped points above 1.12; raw TRIC tick labels could be rounded (e.g. 902.5 shown as 902).

## Before v33
Developed as numbered notebooks in [Biophysics-general](https://github.com/eoinleen/Biophysics-general)
(`Dianthus_*_v2` … `v28`, `Dianthus_TRIC_SS_figure_make_v2`/`v4`). Superseded by this repository.
