# Predicting Lake Mixed-Layer Temperature with Multiple Linear Regression

Modeling winter **lake mixed-layer temperature** on Lake Superior from atmospheric and lake-surface conditions in the ERA5-Land climate reanalysis, using a full multiple-linear-regression workflow: EDA, model selection (stepwise AIC/BIC and Lasso), feature engineering, assumption diagnostics, outlier treatment, and out-of-sample validation.

**Final model:** Adjusted R² = **0.803** · Grouped 10-fold cross-validated R² = **0.801** (RMSE 0.285 °C)

> **Course:** UCLA STAT 152 — Environmental Statistics (Prof. McKinnon) · Group project
> **My role on the team:** _led [variable selection / diagnostics / cross-validation] — **edit this line to match what you actually did**_
> **Language:** R (R Markdown) · **Data:** Copernicus ERA5-Land reanalysis

<p align="center">
  <img src="figures/00-study-area.png" width="600" alt="Lake Superior study region"><br>
  <em>Study region — the Lake Superior basin (Longitude −92.5 to −84.0, Latitude 46.0 to 49.2).</em>
</p>

---

## Research question

**Can we predict lake mixed-layer temperature from atmospheric and lake conditions in Copernicus climate data?**

We model lake mixed-layer temperature as a function of four predictors:

| Predictor | Variable | Description |
|---|---|---|
| `t2m` | 2 m air temperature | Near-surface air temperature |
| `cloud` | Total cloud cover | Fraction, 0–1 |
| `lake_bottom_temp` | Lake bottom temperature | ECMWF lake variable |
| `solar` | Surface solar radiation | Downward shortwave (W·s/m²) |

**Outcome:** `lake_mix_temp` — lake mixed-layer temperature.

## Data

- **Source:** [ERA5-Land reanalysis (GRIB)](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-land?tab=overview), Copernicus Climate Data Store.
- **Region:** Lake Superior — Longitude −92.5 to −84.0, Latitude 46.0 to 49.2.
- **Time index:** 173 timestamps per variable — two winter dates (January 1 and December 1) each year from 1940 through January 2026.
- **Interpretation:** Because only two winter-period timestamps are used per year, results describe a **winter-period model**, not a full annual model.

> The raw `.grib` file is **not committed** to this repository (it is large and redistribution-restricted). Download it from the link above and place it beside the `.Rmd` — see [Reproducing the analysis](#reproducing-the-analysis).

## Method

```
Preprocess → EDA → Full model → Variable selection → Feature engineering
   → Assumption diagnostics → Transformations → Outlier treatment → Cross-validation
```

1. **Preprocessing** — spatially average each raster layer over the region to get one measurement per timestamp; assemble one row per timestamp with the outcome and four predictors; check for missing values.
2. **EDA** — pairwise scatterplot matrix and correlation structure of all variables.
3. **Model selection** — stepwise AIC (forward & backward), stepwise BIC, and Lasso; multicollinearity checked with VIF.
4. **Feature engineering** — two-way interaction terms and quadratic terms, retained only where they improved fit.
5. **Assumption checking** — Residuals-vs-Fitted, Q-Q, Scale-Location, and Residuals-vs-Leverage plots, plus Shapiro–Wilk (normality), Breusch–Pagan (homoscedasticity), and Durbin–Watson (autocorrelation, relevant for this time-indexed data) tests.
6. **Transformations & outliers** — anomaly and Box-Cox transformations evaluated; influential point (observation 118) identified via Cook's distance and removed.
7. **Validation** — **grouped** 10-fold cross-validation, with folds formed by year so the two timestamps from the same year are never split across folds (`set.seed(8901)` for reproducibility).

## Selected results

**EDA — variable fields and relationships**

<p align="center">
  <img src="figures/01-spatial-maps.png" width="720" alt="Spatial fields of the three variables"><br>
  <em>Spatially averaged fields for 2 m temperature, cloud cover, and lake bottom temperature.</em>
</p>

<p align="center">
  <img src="figures/02-pairs-plot.png" width="560" alt="Scatterplot matrix"><br>
  <em>Pairwise relationships among the outcome and predictors.</em>
</p>

**Feature engineering — linear vs. quadratic fits**

<p align="center">
  <img src="figures/03-quadratic-fits.png" width="640" alt="Linear vs quadratic fits per predictor"><br>
  <em>Each predictor against the outcome with linear and quadratic fits — motivating the quadratic term on temperature.</em>
</p>

**Assumption diagnostics & influence**

<p align="center">
  <img src="figures/04-diagnostics.png" width="640" alt="Regression diagnostic plots">
  <img src="figures/05-influence-plot.png" width="330" alt="Influence plot with Cook's distance"><br>
  <em>Left: residual diagnostics for the final model. Right: influence plot — observation 118 flagged by Cook's distance.</em>
</p>

**Final model**

```r
lake_mix_temp ~ t2m + I(t2m^2) + cloud + lake_bottom_temp + solar +
                t2m:lake_bottom_temp + cloud:solar          # observation 118 removed
```

| Metric | Value |
|---|---|
| Multiple R² | 0.811 |
| **Adjusted R²** | **0.803** |
| Residual standard error | 0.289 °C (164 df) |
| F-statistic | 100.4 on 7 and 164 df (p < 2.2e-16) |
| **Cross-validated R²** (grouped 10-fold) | **0.801** |
| Cross-validated RMSE / MAE | 0.285 / 0.222 °C |

All predictors and retained interaction/quadratic terms are significant; full coefficient tables and test output are in the [write-up](environmental_stats_final_writeup.pdf).

## Reproducing the analysis

**Requirements:** R (≥ 4.0) and the packages `terra`, `MASS`, `car`, `caret`, `glmnet`, `lmtest`, `ggplot2`, `gridExtra`.

```r
install.packages(c("terra","MASS","car","caret","glmnet","lmtest","ggplot2","gridExtra"))
```

**Steps**

1. Clone the repository.
2. Download the ERA5-Land GRIB extract for the region/dates above from the [Copernicus CDS](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-land?tab=overview) and place the `.grib` file in the repository root (the `.Rmd` expects it beside itself).
3. Open `environmental_stats_final_project_code.Rmd` in RStudio and **Knit**, or run:

   ```r
   rmarkdown::render("environmental_stats_final_project_code.Rmd")
   ```

> The GRIB filename is set near the top of the `.Rmd`; update that string to match your downloaded file.

## Repository contents

| File | Description |
|---|---|
| `environmental_stats_final_project_code.Rmd` | Full analysis — preprocessing through cross-validation |
| `environmental_stats_final_writeup.pdf` | Written report with full results and interpretation |
| `Slideshow_Presentation_Lake_Temp_Pred_MLR.pdf` | Presentation slides |
| `figures/` | Figures rendered from the analysis (used in this README) |

## License

Released under the [MIT License](LICENSE).
