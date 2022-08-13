# Time-Series

## Rossmann operates over 3,000 drug stores in 7 European countries. Currently, Rossmann store managers are tasked with predicting their daily sales for up to six weeks in advance. Store sales are influenced by many factors, including promotions, competition, school and state holidays, seasonality, and locality. With thousands of individual managers predicting sales based on their unique circumstances, the accuracy of results can be quite varied.

## Context

I will use the Rossmann dataset to create a model that will predict that last 90 days of sales.

## Data
For this project, I will choose store #5 sales by random. After removing the other features and focusing on store # 5 the dataframe contains 942 rows of sales data.

Below are the measures of central tendency:

Sales
count	942.000000
mean	3867.110403
std	2389.609890
min	0.000000
25%	2027.000000
50%	4180.000000
75%	5419.250000
max	11692.000000

Subsequent Data Analysis revealed the following:
- No null data.
- There seems to be alot of zero sales. This must be attributed to weekend/holiday store closure.
- The data otherwise seems very flat with a lot of variance.
- The distribution is also skewed to the right.
- The minimum sale is 0 and the maximum is 11692.
- The store is closed every Sunday. Therefore, there is an underlying seasonal pattern that every 7th day there are zero sales. I will have period = 7 for my time series decomposition and forecast modeling.

## Seasonal Decomposition
- The estimated seasonal component strongly suggests a predictable pattern and reinforces the premise that the time-series is not stationary.
## Stationary Statistical Testing
ADF Statistic: -5.2250881457262945
p-value: 7.812279185012506e-06
n_lags: 20
nobs:921
Critical Values:
   1%, -3.437470108019385
Critical Values:
   5%, -2.8646832620852853
Critical Values:
   10%, -2.5684436698650503
   
KPSS Statistic: 0.11499588513461094
p-value: 0.1
num lags: 22
Critial Values:
   10% : 0.347
   5% : 0.463
   2.5% : 0.574
   1% : 0.739
Result: The series is stationary

Both the ADF & KPSS statistical tests find that the time series is stationary

- The correlation plots above give me the impression that both seasonal and non-seasonal integration parameters are higher than 1 and if I am reading them correctly (the number of lags before it decays to 0) then both are 5. However, this would create a very complex model. Therefore, I will apply a value of 0 and 1 to both of these parameters.

- Since both statistical tests confirm that data is stationary, I will continue but the statistical tests (the KPSS nearly rejected the null hypothesis) and plots (the autocorrelation plot does not entirely decay to 0) lead me to believe that there may be a gray zone between stationary and non-stationary. Especially since the original data and the decomposed estimated seasonal plot strongly suggest a seasonal pattern and, therfore, non-stationarity.

- I returned these autocorrelation and partial correlation plots and increased their lags to 500 and 130, respectively. Both plots do ultimately decay to 0

## Modeling & Optimization

Here I will use a function that will test different parameters for the SARIMAX model. This function will create a table with the best performing model. I will also input seasonal parameter as 0 and non-seasonal integration parameters to be 1 and period will be 7 due to the store being closed every Sunday.


   (p,q)x(P,Q)	AIC
0	(1, 1, 1, 1)	14606.149250
1	(1, 0, 1, 1)	14621.763917
2	(1, 1, 0, 1)	14639.846620
3	(0, 1, 1, 1)	14640.477726
4	(1, 0, 0, 1)	14655.386360
5	(0, 1, 0, 1)	14682.480130
6	(0, 0, 1, 1)	14695.072590
7	(0, 0, 0, 1)	14766.788213
8	(1, 1, 1, 0)	14808.655837
9	(1, 0, 1, 0)	14822.673812
10	(0, 1, 1, 0)	14838.699413
11	(0, 0, 1, 0)	14887.577260
12	(1, 1, 0, 0)	15331.919276
13	(1, 0, 0, 0)	15345.523188
14	(0, 1, 0, 0)	15391.924758
15	(0, 0, 0, 0)	15503.257974

The above table demonstrates a significant difference in the AIC score given a p,q, P, Q values of 1,0,1,0, respectfully. Seasonal and non-seasonal parameters are the following: d=0 and D=1.

The model used is the following: SARIMAX(1,0,1) X (1, 1, 1, 7).

Mean Absolute Error 827.6904255925241
Root Mean Squared Error 1227.9805636421988
