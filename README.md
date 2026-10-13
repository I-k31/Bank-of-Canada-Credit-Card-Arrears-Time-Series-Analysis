# Bank of Canada Credit Card Arrears: Time-Series Analysis

## Research question

Does a change in the 5-year mortgage rate predict a change in credit card arrears three quarters later?

## Data

Data were retrieved from the Bank of Canada Valet API.

| Variable | Series ID | Frequency used |
|---|---|---|
| Credit card arrears | `FVI_LOANS_ARR_CC` | Quarterly |
| Overnight rate | `V39079` | Daily observations averaged by quarter |
| 5-year conventional mortgage rate | `V80691335` | Monthly observations averaged by quarter |
| CPI-trim | `CPI_TRIM` | Monthly observations averaged by quarter |

The merged dataset contains 45 quarterly observations from 2015 Q1 to 2026 Q1. Household credit (`V36415`) was not included in the merged dataset because its available history ends in 2020.

## Exploratory analysis

The correlation between credit card arrears and the 5-year mortgage rate in levels is approximately 0.61. The overnight rate and 5-year mortgage rate are highly correlated (approximately 0.97). CPI-trim has little correlation with credit card arrears in levels (approximately -0.05).

The lag-correlation comparison showed the highest correlation between arrears and the 5-year mortgage rate at a three-quarter lag (approximately 0.645). This lag was selected for the regression analysis based on that exploratory comparison.

## Method

1. Apply the Augmented Dickey–Fuller (ADF) test to each original series.
2. First-difference the series and apply the ADF test again.
3. Fit an OLS regression of the change in credit card arrears on the change in the 5-year mortgage rate lagged by three quarters.
4. Review the coefficient, p-value, confidence interval, fit statistics, and residual diagnostics.

The ADF tests on the original series did not reject a unit root at the 5% level. After first differencing, the ADF tests rejected a unit root at the 5% level for each series. This is evidence consistent with stationarity, rather than proof that the series are stationary.

## Final regression results

The final differenced regression uses 41 quarterly observations.

| Statistic | Result |
|---|---:|
| Mortgage-rate change coefficient (3-quarter lag) | 0.027 |
| Standard error | 0.029 |
| p-value | 0.363 |
| 95% confidence interval | [-0.032, 0.087] |
| R² | 0.021 |
| Adjusted R² | -0.004 |
| Durbin–Watson statistic | 1.857 |

The estimated coefficient is positive, but it is not statistically significant at the 5% level. The confidence interval includes zero, and the model explains little of the variation in quarterly changes in credit card arrears.

## Limitations

- The analysis uses a relatively small sample of quarterly observations.
- The regression includes only one predictor.
- The three-quarter lag was selected using correlations in the original series; the lag was not re-selected using the differenced series.
- The final model's residuals are not normally distributed (Jarque–Bera statistic approximately 105.2, p-value approximately 1.4 × 10⁻²³; skewness approximately -2.2; kurtosis approximately 9.5).
- The analysis does not include an out-of-sample evaluation.
- The exploratory levels regression has a low Durbin–Watson statistic (approximately 0.25), indicating substantial positive residual autocorrelation; its usual OLS inference should therefore be treated cautiously.

## Possible next steps

- Inspect quarters with unusually large changes.
- Revisit the lag choice and assess it using the differenced series.
- Consider Newey–West standard errors.
- Explore additional predictors or alternative time-series models, such as a distributed-lag model or VAR.
- Evaluate the model on held-out, more recent quarters.

## Tools

- Python
- pandas
- matplotlib
- statsmodels
- Bank of Canada Valet API
