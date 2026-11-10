# Input data format

The notebook reads two layouts. Upload any mix of them in one run. Example files for both are in [`examples/`](../examples/); if your file looks like those, it will load.

---

## 1. Wide sheet (your own layout)

One file = one plate, one readout. `.xlsx` (**first worksheet only**) or `.csv`.

|   | A | B | C | D | E | F | … |
|---|---|---|---|---|---|---|---|
| **1** | Binder A | | Binder A | | Binder B | | |
| **2** | [EL17] (nM) | Ratio | [EL17] (nM) | Ratio | [EL17] (nM) | Ratio | |
| **3** | 20000 | 1.0633 | 20000 | 1.0621 | 20000 | 1.0381 | |
| **4** | 6666.7 | 1.0609 | 6666.7 | 1.0605 | 6666.7 | 1.0412 | |
| … | … | … | … | … | … | … | |

**Columns** come in pairs: concentration, then signal (A+B, C+D, E+F, …). A last unpaired column is ignored.

**Row 1: sample name**, written above the concentration column (or the signal column; either works).
- Pairs with the **same name are repeats** and are fitted together. The match is exact: `K11` and `K11 ` are the same (spaces are trimmed), but `K11` and `k11` are two samples.
- An empty name becomes `Sample 1`, `Sample 2`, and so on.
- Repeats are pooled **within one file only**. The same name in two different wide files gives two separate analyses.

**Row 2: optional.** Column headers, blank, or already data. Any cell that isn't a number is skipped wherever it appears.

**Concentration**
- In **nM**. Wide sheets get no unit conversion, so µM values would be read as nM.
- Any order (sorted internally). Repeats may use different concentrations and different numbers of points.
- 0 is allowed: it's used in the fit but can't be shown on the log axis. Negative values are dropped.

**Signal**
- Spectral shift: the 670/650 ratio (values around 1).
- TRIC: F<sub>norm</sub> in ‰, as exported (values around 900).
- The readout is detected **per file** from the median signal: below 10 means ratio, otherwise TRIC. Don't put both readouts in one file.

**Missing values:** leave the cell empty. A row with either value missing is dropped for that pair only.

---

## 2. Dianthus software export table

One file = **one repeat** of one sample, exactly as the instrument software exports it. `.xlsx` or `.csv`. The file is recognised by a column named exactly `Ligand Conc.` in its first row.

| Column | Required | Use |
|---|---|---|
| `Ligand Conc.` | yes | concentration |
| `Ligand Conc. Unit` | no | `pM`, `nM`, `µM`/`uM`, `mM`. If the column is absent, nM is assumed. ⚠ Any other unit text currently drops that row without a warning |
| `Ratio` | one of these two | spectral-shift signal |
| a column containing `norm` (e.g. `Fnorm`) | one of these two | TRIC signal |
| `Anomaly` | no | `-` = OK. Anything else counts as flagged; flagged wells are dropped only with `DROP_ANOMALIES = True` |

Other columns (`Well ID`, `Initial Fluorescence`, …) are ignored.

**The sample name comes from the file name:** a leading `table-` is removed, then any trailing `-<number>` groups.

| File name | Sample |
|---|---|
| `table-K11-EL17-1.xlsx` | `K11-EL17` |
| `table-K11-EL17-1-2.xlsx` | `K11-EL17` |
| `table-K33-EL17-4.xlsx` | `K33-EL17` |
| ⚠ `table-diUb-48-1.xlsx` | `diUb`: the `-48` is removed too |

So **don't end a sample name with `-<number>`**. Write `K48diUb`, not `diUb-48`.

All export tables of the same readout in one upload are **pooled**: files that give the same sample name become repeats of that sample.

---

## Settings that change what is read
| Setting | Effect |
|---|---|
| `MAX_CONC_NM` | Wells above this concentration are ignored, for every sample |
| `DROP_ANOMALIES` | Export tables: drop wells flagged in `Anomaly` |
| `SAMPLE_LABELS` | Rename samples after loading. Two samples given the same new name are pooled |

## Checking that it read what you meant
Each run prints a table per file with `n_repeats` for every sample, and `results.csv` adds `n_points`. Compare both with your plate layout before you look at any Kd. A missing repeat or points almost always means a naming or unit problem.
