Bank of Canada Credit Card Arrears: Time-Series Analysis

Does a change in the 5-year mortgage rate predict a change in credit card arrears three quarters later?

I pulled Bank of Canada data through the Valet API, built a clean quarterly dataset, tested the series for stationarity, and fit an OLS regression on differenced data. Short answer: no significant relationship. The two series look strongly related in levels (correlation of about 0.65), but that link disappears once the trends are removed.

Key result
	
Model	OLS: change in arrears on change in 5-year mortgage rate, lagged 3 quarters
Observations	41 quarters
Mortgage-rate coefficient	0.027 (std. error 0.029)
p-value	0.363
95% confidence interval	[-0.032, 0.087]
R-squared / adjusted	0.021 / -0.004
Durbin-Watson	1.857

The confidence interval includes zero, and the model explains about 2% of the variation in quarterly changes in arrears. In levels, arrears and the lagged mortgage rate correlate at about 0.65, but both series trend over the period, so that correlation says little about a real relationship. Differencing removes the trends, and the relationship goes away.

Data

All series come from the Bank of Canada Valet API.

Variable	Series ID	Frequency
Credit card arrears rate (%)	FVI_LOANS_ARR_CC	Quarterly
Overnight rate (%)	V39079	Daily, averaged to quarterly
5-year conventional mortgage rate (%)	V80691335	Monthly, averaged to quarterly
CPI-trim (%)	CPI_TRIM	Monthly, averaged to quarterly

The merged dataset has 45 quarterly observations, from 2015 Q1 to 2026 Q1. The arrears series is the limiting factor, since it only starts in 2015.

I also looked at household credit (V36415) but dropped it because its history ends in 2020.

Method
Discover and pull series. I searched the Valet series list for arrears, interest rates and inflation measures, then pulled the observations for each series.
Build the dataset. I resampled the higher-frequency series to quarterly averages and merged them with the arrears series into one 45-row table.
Explore. Time-series plots (with the 2020 and 2022-23 periods shaded), a correlation matrix, scatter plots, and lag correlations.
Choose variables.
The overnight rate and the 5-year mortgage rate are almost the same signal in this sample (correlation of 0.97), so using both would be redundant. I kept the mortgage rate, which is the more natural variable for consumer credit.
CPI-trim was dropped because its correlation with arrears was weak at the lags I tested.
Test for stationarity. Augmented Dickey-Fuller tests on each series, then first-order differencing, which made the series stationary.
Regression. Difference every series, shift the mortgage rate back 3 quarters, and fit OLS with statsmodels. I chose the 3-quarter lag from the lag-correlation table.
Limitations
Small sample. 41 usable observations and one predictor. The test has little power, so "not significant" means "no evidence of an effect here", not "no effect".
One predictor. I did not model other drivers of arrears, such as unemployment, household debt or income.
Lag choice. I picked the lag from correlations on the raw levels, where the correlations for lags 1 to 4 are almost identical (0.63 to 0.65). I haven't tested other lags on the differenced data.
Residuals are not normal. The Jarque-Bera test strongly rejects normality (skew of -2.2, kurtosis of 9.5), which suggests a few extreme quarters. The p-values assume roughly normal errors, so they should be treated with caution.
No out-of-sample test. I did not evaluate the model's forecasts on held-out data, and I make no claim about predictive performance.
Next steps
Identify the extreme quarters (likely around 2020) and re-run without them or with a robust method.
Re-check the lag choice on the differenced series.
Report standard errors that allow for autocorrelation (Newey-West).
Add other predictors and try a model built for time series, such as a distributed-lag or VAR model.
Hold out the most recent quarters and compare the model against a no-change forecast.
How to run
bash
pip install requests pandas matplotlib statsmodels
jupyter notebook TimeSeries.ipynb

Run the notebook from top to bottom. It pulls live data from the Valet API, so results can shift slightly if the Bank of Canada revises its series. The results above are from a run on 19 August 2026.

Tools

Python, pandas, matplotlib, statsmodels, Bank of Canada Valet API
