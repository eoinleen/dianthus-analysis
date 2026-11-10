# Dianthus
More adaptable tool for Dianthus TRIC and spectral shift fitting. Uses the Morrison quadratic 1:1, Hill fit and 2:2 fitting

Dianthus TRIC / spectral-shift analysis — v33
Run order: settings cell → analysis cell → upload/run cell. After changing a setting, re-run the settings cell and the upload/run cell (the analysis cell only needs re-running if you edit the code).
Inputs (both kinds can go in one run)
Layout	What it looks like	How repeats are grouped
Wide sheet	Row 1 = sample names, one per column pair; columns alternate conc. (nM) / signal. A header row under the names is optional.	Column pairs with the same name
Dianthus export table	The instrument's own table (Well ID, Ligand Conc., Ligand Conc. Unit, Ratio or Fnorm, Anomaly) — one file per repeat	File name minus table- and trailing -<number>s: table-K11-EL17-3.xlsx → K11-EL17. All export files of one readout are pooled.
TRIC vs ratio is detected from the signal size, so files don't need naming.
Settings you will actually change
Setting	Use
TOTAL_TARGET_NM	Total tracer in the well (labelled + unlabelled), nM
FIT_MODEL	"1:1" (default) or "two_site". Decide before looking at the data.
TWO_SITE_KD2_NM	Fix the weak-site Kd (nM) when the top concentration can't define it, e.g. from an independent experiment. None = fitted.
MAX_CONC_NM	Ignore wells above this concentration (all samples; state it as a rule). None = use all.
DROP_ANOMALIES	Export tables only: drop wells the instrument flagged in Anomaly. Default False.
HILL_FIGURES	True = also write a hill/ folder: panels, grid and overlays with the Hill fit (EC50 ± CI, n_H) and the classic linearised Hill plot (log[θ/(1−θ)] vs log[L]; slope = n_H, zero crossing = EC50). Hill panels, overlay and Hill plot are appended to the main PPTX after the reported-model slides. Descriptive only; roughly doubles run time.
X_SCI_FROM	x-axis labels plain below this (nM), 10ⁿ from it. Default 1000 → 0.1 1 10 100 10³ 10⁴.
Main figure: Figure_rows.pdf / .svg / .png
One row per readout (TRIC row, then spectral-shift row), two panels per row: fraction bound with the legend ("K11 (K_D = 187 ± 50 nM)" / "n = 5, R² = 0.99"), then the raw signal. It is exactly PAGE_WIDTH_MM wide (default 180 mm, an A4 page with margins) and every data box is identical, so place it at 100% and the fonts are the sizes set in AESTHETICS. With HILL_FIGURES = True there is a matching Figure_rows_hill. Figure_rows.pptx holds both at true size on A4 slides. The two panels also exist as separate files in each readout folder (overlay_panel, overlay_raw), same box size.
Changing figure text
All text on the figures lives in the LABELS block of the settings cell: sample display names (SAMPLE_LABELS), axis labels, the Kd / EC50 / n_H symbols, the representative-panel title, panel titles on/off, and whether legends show n and R².
Reading results.csv
Kd_nM, Kd_CI95_low/high: the reported model (Kd1 for two-site), with residual-bootstrap 95% CI.
Two-site extras: Kd2_nM (+CI if fitted), frac_tight_site = fraction of the signal change from the tight site.
Diagnostics, always run: dAICc_1to1, dAICc_nonspecific, dAICc_hill, dAICc_twosite = AICc(reported) − AICc(alternative). Positive = the alternative fits better; > 6 = strongly. Also hill_EC50_nM, hill_n (Dianthus-software-style Hill; with HILL_FIGURES also their 95% CIs), and twosite_Kd1_nM, twosite_Kd2_nM, twosite_frac_tight.
Flags worth acting on: two-site strongly favoured (consider FIT_MODEL = "two_site" for the next experiment's pre-specified analysis), two-site not resolved by these data (Kd2 ran beyond the data — fix TWO_SITE_KD2_NM or stay with 1:1), Kd2 near/above top concentration.
Changes from v32
Two-site model (reported or diagnostic) · optional Hill figure set incl. linearised Hill plot · methods text states any excluded wells · Hill EC50/n saved · Dianthus export tables read directly · MAX_CONC_NM · x-axis 10ⁿ from X_SCI_FROM · header rows detected automatically (v32 silently dropped the first data row of sheets without a header row) · TOTAL_TARGET_NM re-read on every run · fraction-bound axes widen instead of hiding points; Kd box top-left on fraction-bound panels · overlay legend in Kd order · bad fits no longer carry contradictory flags.
