# Treasury Yield Prediction

A statistical analysis of macroeconomic and financial determinants of the
US 10-Year Treasury Yield, using monthly data from January 2015 to December
2024. Combines a Python data pipeline with R-based econometric modeling.

**The full writeup is in [`Final_Paper.pdf`](./Final_Paper.pdf).**

## Question

What macroeconomic and financial factors explain monthly variation in the
US 10-Year Treasury Yield? Past research on this topic has focused mostly
on conventional drivers — interest rates, inflation, GDP growth. This
project also tests less conventional predictors: gold prices, the US
Dollar Index, foreign bond yields (China 10Y), and the unemployment rate.

## Data

Seven monthly time series, January 2015 through December 2024
(120 observations). All sources are publicly available:

| Variable | Source | Frequency |
|---|---|---|
| US 10Y Treasury Yield (response) | FRED (DGS10) | Daily → month-end |
| Federal Funds Rate | FRED (FEDFUNDS) | Monthly |
| CPI / Inflation Rate | FRED (CPIAUCSL) | Monthly |
| Unemployment Rate | FRED (UNRATE) | Monthly |
| S&P 500 | Yahoo Finance (^GSPC) | Daily → month-end |
| Gold prices | Yahoo Finance (GC=F) | Daily → month-end |
| USD Index | Yahoo Finance (DX-Y.NYB) | Daily → month-end |
| China 10Y Yield | Wall Street Journal | Daily → month-end |

Daily series were resampled to month-end. FRED's monthly series, which are
dated at the start of each reporting month, were aligned to the month-end
convention so all sources merge cleanly. The merged dataset has zero
missing values.

## Methods

**Data pipeline (Python):** Retrieves data from yfinance and FRED CSV
exports, aligns dates to month-end, computes the inflation rate from CPI
levels, and merges all sources into a single monthly panel.

**Modeling (R):**
- Multiple linear regression of US 10Y on the seven predictors plus a COVID
  dummy variable (March 2020 – June 2021)
- Feature engineering: lagged inflation (2-month lag, to reflect the delay
  in how markets price in inflation news) and differenced S&P 500 (to
  capture short-term equity fluctuations rather than absolute level)
- Multicollinearity check via Variance Inflation Factors (all VIFs < 4)
- Durbin-Watson test for residual autocorrelation (DW = 0.36, indicating
  strong positive autocorrelation)
- Newey-West HAC standard errors to correct for the autocorrelation
- Interaction model exploring whether the unemployment-yield relationship
  shifted during the COVID period

## Findings

After applying Newey-West HAC corrections, the **federal funds rate is
the only predictor that remains statistically significant at the 5%
level**. Several variables (USD index, unemployment, the COVID dummy, the
unemployment × COVID interaction) appeared significant in the uncorrected
OLS model but lost significance once autocorrelation was addressed,
suggesting their original significance was overestimated.

The dominance of the federal funds rate likely reflects the historical
period sampled — including the zero-lower-bound period of 2020–2022 —
during which markets placed unusual weight on short-term policy signals
when forming long-term yield expectations. The lack of significance for
inflation does not imply that inflation is irrelevant; rather, its effect
is likely already embedded in monetary-policy decisions that the federal
funds rate captures.

The model explains 88.4% of variation in US 10Y yields (R² = 0.884).

## Limitations

- **Sample size (n = 120).** Limits the number of predictors that can be
  reliably estimated and reduces statistical power.
- **In-sample only.** No train/test split or out-of-sample validation;
  the analysis is inferential rather than predictive.
- **Linear specification.** Cannot capture nonlinear or regime-dependent
  relationships, which may matter for predictors like inflation and
  equity returns.
- **Structural break around COVID.** The dummy variable is a coarse fix
  for what was likely a more complex shift in market behavior.

## Repository structure

- [`Final_Paper.pdf`](./Final_Paper.pdf) — full writeup with literature
  review, methodology, results, and discussion
- [`notebooks/`](./notebooks/) — Python data pipeline (`.ipynb`)
- [`R/`](./R/) — R Markdown source and knitted output for the
  econometric analysis
- [`data/`](./data/) — merged dataset (`Merged_Data.csv`)

## Tools

Python (pandas, yfinance, numpy), R (tidyverse, lmtest, sandwich,
regclass, ggplot2)

## Notes

- The Python notebook was developed in Google Colab; some file paths
  reflect that environment.
- The R Markdown file (`treasury_stat1223v3.Rmd`) is the source of truth
  for the analysis. The included HTML render is from a partial knit;
  refer to `Final_Paper.pdf` for the complete results.

## Author

Rita Zhang
