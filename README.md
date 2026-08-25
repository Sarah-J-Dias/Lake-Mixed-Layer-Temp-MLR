# Lake-Mixed-Layer-Temp-MLR
This repository contains the R code and corresponding slideshow presentation of a project for my Environmental Stats class in which my group predicted Lake Mixed-Layer Temperature using MLR.

# Research Question and Variables of Interest
Can we predict lake mixed-layer temperature using atmospheric and lake conditions from Copernicus climate data? We use multiple linear regression to model lake mixed-layer temperature as a function of 2m air temperature, total cloud cover, lake bottom temperature, and surface solar radiation.

# Dataset
The dataset used was the **ERA5-Land GRIB** over the Lake Superior region (Longitude: -92.5 to -84.0, Latitude: 46.0 to 49.2). The extracted time index contained 173 timestamps per variable. The timestamps were January 1 and December 1, beginning in 1940 and continuing through January 2026. Because we included only two winter-period timestamps per year, our analysis should be interpreted as a winter-period prediction model rather than a full annual model.

**ERA5-Land GRIB Documentation and Download Link:** https://cds.climate.copernicus.eu/datasets/reanalysis-era5-land?tab=overview
# Files
-
-
# ML PIPELINE
## Data Pre-processing
We averaged the raster layers to create one measurement per timestamp and then created a final dataframe that contained one row per time step and six columns containing lake mixed-layer temperature (outcome variable) and predictor variables.

## EDA
- Correlation Matrix
- Scatterplot Matrix
- Baseline Full Model (lm(lake_mix_temp ~ t2m + cloud + lake_bottom_temp + solar)
- VIF to check for multicollinearity

## Variable Selection
-Stepwise AIC
-Stepwise BIC
-Lasso

## Feature Engineering
-Interaction Terms
-Quadratic Terms

## Checking Model Assumptions
-Residuals vs Fitted Plot
-Q-Q plot
-Scale-Location
-Residuals-Leverage (Cook's Distance)

## Transformations and Checking for Outliers
-Anomaly transformation
-Box-Cox transformation

## Final Model 
-Adjusted AIC: 71.20164 (smallest)!; Adj. R^2: 0.8027; RSE: 0.2892 (smallest)

## Out-of-Sample Validation
-grouped 10-fold cross-validation
-R^2 = 0.804

#
