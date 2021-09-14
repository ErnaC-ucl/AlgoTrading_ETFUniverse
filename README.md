# Pairs trading based on co-integration for ETF investment universe 

Here I've used a pairs trading strategy based on the co-integration of ETFs, combined with momentum indicators. 

##The principle is as follows:

1. Let's say you have a pair of securities X and Y that have some underlying economic link.(This is especially relevant for sectoral ETFs that consist of a diversified portfolio of assets of the same industry).

2. These time series may not be independently stationary, but they may have a relationship where the difference tends to be stationary. In this case the time-series are considered co-integrated. 

3. You expect the spread between these two to remain constant with time, if the stationary test generates statistically significant results. 

4. However, from time to time, there might be a divergence in the spread between these two pairs. When there is a temporary divergence between the two securities, the pairs trade consists of, betting that the "spread" between the two would eventually converge.

**Technical Implementation of the strategy:**

1. After some data cleaning and pre-processing I run multiple OLS models between pairs to observe whether linear regression models are a good fit to describe the evolution of log prices of ETFs.

2. The aim is to use the resulting beta of the OLS regressions as the hedge ratio to create the spread series. Based on initial visual checks most of the spreads seem stationary but we'll run a robust statistical method to check their stationarity (Augmented Dickey-Fuller Test)

3. ADF tests the null hypothesis that a unit root is present in a time series sample. The alternative hypothesis is stationarity. The augmented Dickey–Fuller (ADF) statistic, used in the test, is a negative number. The more negative it is, the stronger the rejection of the hypothesis that there is a unit root at some level of confidence. The intuition behind the test is that if the series is characterised by a unit root process then the lagged level of the series  will provide no relevant information in predicting the change. In contrast, when the process has no unit root, it is stationary and hence exhibits reversion to the mean - so the lagged level will provide relevant information in predicting the change of the series and the null of a unit root will be rejected.

4. I've used this intuition to filter the pairs whose spread series, suggests that we can reject the null hypothesis with a significance level of less than 1%. Rejecting the null hypothesis means that the process has no unit root, and in turn that the time series is stationary or does not have time-dependent structure.

5. I've used this approach to generate trading signals. If the sample is out of the range defined by +/- 1.96 standard deviations away from the mean, then we think there is a price divergence between these two asset prices which means the pairs trading opportunity is present. 

6. I've added an extra filter based on momentum indicators to the buy/sell trading signals generated above. This combination would ensure that our trading signal is in line with the price trend. Therefore, this algorithm simultaneously checks whether:
  i. the instrument for which pairs trading strategy has indicated a buy signal, has a positive momentum (on average) -> if so, final insight is a buy 
 ii. the instrument for which pairs trading strategy has indicated a sell signal, has a negative momentum (on average) -> if so, final insight is a sell

If both conditions are met, pairs trading is executed 

**Why pairs trading makes sense for ETFs:**

1. ETFs are designed to track a well-diversified portfolio of assets. For this reason, ETFs should have much less idiosyncratic risk than individual common stocks. It has been shown that pairs trades are less profitable when the original price deviation is associated with a fundamental change in the value of the individual stocks. But the strategy is signficantly profitable when the cause of the price divergence is related to temporary liquidity factors, or to news that affects systematically the underlying assets in a basket (which is the case for ETFs especially the ones that cover a given sector). 

*Source: Engelberg, Gao, and Jagannathan (2009)*
This study also extends the analysis to test for the optimal time to exit from a pairs trade, and finds that *it is more profitable to close out positions after 10 days if the spread of the pairs doesn't converge**

2. As prices in a pair of ETFs were closely cointegrated in the past, there is a high probability those two securities share common sources of fundamental return correlations. A temporary shock could move one ETF out of the common price band. This presents a statistical arbitrage opportunity. The universe of pairs is continuously updated, which ensures that pairs which no longer move in synchronicity are removed from trading, and only pairs with a high probability of convergence remain.

3. ETFs can be sold short more easily than stocks because the uptick rule doesn’t apply to them. For this reason, it may be easier to employ a pairs trading strategy on ETFs than on stocks.
