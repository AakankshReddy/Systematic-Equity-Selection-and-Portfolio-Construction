# Factor-Based Portfolio Construction Using Historical Risk-Adjusted Returns

## Overview

This project develops and evaluates a systematic equity portfolio construction framework for Indian equities using only historical information available at the time of investment.

The objective is to answer a simple question:

> Can a portfolio constructed from historical risk-adjusted performance outperform both a passive market benchmark and a naïve equal-weight allocation without using machine learning, analyst forecasts, or future information?

To investigate this, a factor-based ranking model was developed and tested using a walk-forward backtesting framework spanning 2010–2024.

The final model ranks stocks using a combination of:

* CAGR (Compound Annual Growth Rate)
* Sharpe Ratio
* Volatility Scaling

and allocates capital only to the highest-ranked securities.

All portfolio decisions are made exclusively from historical information available at the rebalance date, eliminating look-ahead bias.

---

# Problem Statement

Most retail investors face two common approaches:

### Passive Investing

Invest equally across stocks or invest in an index fund.

Advantages:

* Simple
* Diversified
* Low maintenance

Disadvantages:

* No preference for stronger businesses
* Capital allocated equally regardless of quality

---

### Stock Picking

Select stocks manually.

Advantages:

* Potential for higher returns

Disadvantages:

* Subjective
* Difficult to scale
* Prone to emotional decision-making

---

The goal of this project is to create a systematic framework that:

* Ranks stocks objectively
* Allocates capital according to historical performance
* Avoids discretionary decision-making
* Remains interpretable
* Can be tested rigorously

---

# Why Not Machine Learning?

Machine learning was intentionally excluded from this project.

The dataset consists of approximately:

* 60–90 equities
* Monthly rebalancing
* Roughly 14 years of data

This produces relatively few independent training samples.

In such settings:

* Complex models easily overfit
* Feature importance becomes difficult to interpret
* Backtests frequently appear impressive but fail out-of-sample

Instead, this project focuses on:

* Transparent factor construction
* Economic intuition
* Robust walk-forward validation

The objective is not prediction.

The objective is portfolio construction.

---

# Data Universe



The universe consists of large-cap and established Indian equities spanning:

* Financials
* Information Technology
* Consumer Staples
* Consumer Discretionary
* Industrials
* Capital Goods
* Energy
* Utilities
* Materials
* Metals
* Healthcare
* Telecom

Data Source:

* Yahoo Finance

Period:

* January 2010 – January 2024

Price Used:

* Adjusted Close Prices

Frequency:

* Daily

---

# Return Computation

Daily returns are calculated as:

$$R_t = \frac{P_t}{P_{t-1}} - 1$$


**Where:**

* **$P_t$** is the price at the end of period $t$ (current price).

---

# Portfolio Construction Methodology

## Step 1: Rolling Historical Window

At each rebalance date:

* Use previous 252 trading days
* Ignore all future data

252 trading days approximately corresponds to:

* One trading year

This window was selected after testing multiple alternatives.

---

## Step 2: CAGR Calculation

For every stock:

### Compound Annual Growth Rate (CAGR)

To measure the mean annual growth rate of an investment over a specified period longer than one year, we use the Compound Annual Growth Rate (CAGR):

$$CAGR = \left( \frac{V_{\text{end}}}{V_{\text{start}}} \right)^{\frac{1}{n}} - 1$$

**Where:**
* **$V_{\text{end}}$** is the ending value of the investment.
* **$V_{\text{start}}$** is the beginning (starting) value of the investment.
* **$n$** is the number of years in the lookback period.

CAGR measures sustained historical growth.

---

## Step 3: Sharpe Ratio Calculation


#### 1. Annualized Volatility

$$\sigma = \text{Std}(\text{Returns}) \times \sqrt{252}$$

**Where:**
* **$\sigma$** is the annualized volatility (standard deviation of returns).
* **$\text{Std}(\text{Returns})$** is the standard deviation of the asset's daily returns.
* **$252$** represents the typical number of trading days in a year used to annualize daily risk.

#### 2. Sharpe Ratio

$$\text{Sharpe} = \frac{\text{CAGR} - R_f}{\sigma}$$

**Where:**
* **$\text{Sharpe}$** is the Sharpe Ratio, measuring excess return per unit of deviation.
* **$\text{CAGR}$** is the Compound Annual Growth Rate.
* **$R_f$** is the risk-free rate of return (e.g., $R_f = 6\%$, representing the baseline return of a riskless asset like government bonds).
* **$\sigma$** is the annualized volatility.

---

## Step 4: Normalization

Raw metrics have different scales.

Therefore:

$$\text{Normalized Score} = \frac{\text{Metric}}{\text{Metric}_{\max}}$$

Negative values are clipped to zero.

This prevents poor performers from receiving positive allocation.

---

## Step 5: Factor Score

The final ranking score is:

$$\text{Score} = \left(\text{Sharpe}_{\text{norm}}\right)^2 \times \text{CAGR}_{\text{norm}}$$

---

# Why This Formula?

Several alternatives were tested:

### Sharpe × CAGR

Highest risk-adjusted performance.

However:

* Underweighted exceptional performers
* Produced more conservative allocations

---

### Sharpe² × CAGR

Final Selection.

Advantages:

* Better balance between return and risk
* Strong out-of-sample performance
* More stable allocations

---

### Sharpe⁴ × CAGR

Produced highly concentrated portfolios.

Issues:

* Excessive dependence on a small number of stocks
* Higher volatility
* Larger drawdowns

---

The Sharpe² formulation provided the best overall trade-off between:

* Return
* Risk
* Diversification

---

# Step 6: Top-15 Selection

At every rebalance:

* Rank all stocks by score
* Select only the 15 highest-scoring names

All remaining securities receive zero allocation.

This creates a focused portfolio while maintaining diversification.

---

# Step 7: Volatility Scaling

Selected stocks are weighted using inverse volatility:

$$w_i \propto \frac{\text{Score}_i}{\sigma_i}$$

is annualized volatility.

This reduces exposure to extremely volatile securities.

---

# Step 8: Weight Normalization

Final weights satisfy:

$$\sum_{i} w_i = 1$$

ensuring full capital deployment.

---

# Walk-Forward Backtest

## Rebalancing Frequency

Every:

* 21 trading days

approximately one month.

---

## Process

For each rebalance:

1. Use previous 252 days
2. Compute scores
3. Select top 15 stocks
4. Construct portfolio
5. Hold for 21 days
6. Repeat

This process continues throughout the entire dataset.

---

# Look-Ahead Bias Investigation

An early version of the project unintentionally used full-sample statistics when constructing weights.

This introduced look-ahead bias.

The issue was identified during validation and corrected.

The final implementation:

* Uses only historical information
* Recomputes scores at every rebalance
* Never accesses future returns

This correction significantly improved the credibility of the results.

---

# Concentration Analysis

A key question was:

> Is the strategy successful because of stock selection or because of concentration?

To answer this, multiple position-cap experiments were performed.

Examples:

| Maximum Position | CAGR |
| ---------------- | ---- |
| 65%              | ~40% |
| 45%              | ~38% |
| 35%              | ~32% |
| 25%              | ~24% |

Observations:

* Performance remained strong under moderate concentration limits
* Performance deteriorated under aggressive diversification
* Conviction-weighted allocation contributes meaningfully to returns

This suggests that both:

* Factor selection
* Capital allocation

are important components of performance.

---

# Benchmark Comparison

The strategy was compared against:

### Equal Weight Portfolio

All stocks receive identical weights.

### Nifty 50

Market benchmark.

---

Example Results

| Portfolio    |   CAGR | Sharpe |
| ------------ | -----: | -----: |
| Strategy     | 29.50% |   1.19 |
| Equal Weight | 22.55% |   1.06 |
| Nifty 50     | 12.69% |   0.40 |

The strategy outperformed both benchmarks over the testing period.

---

# Key Findings

1. Historical risk-adjusted performance contains useful information.
2. Concentrated allocations outperform equal-weight allocations.
3. Walk-forward validation is essential.
4. Simpler factor models can outperform more complex approaches.
5. Portfolio construction matters as much as stock selection.

---

# Limitations

## Survivorship Bias

The universe consists primarily of stocks with long available trading histories.

Consequently:

* Some failed or delisted firms are absent
* Historical performance may be overstated

Future work should evaluate the strategy using historical index constituents.

---

## Transaction Costs

Brokerage fees, slippage, and taxes are not modeled.

Real-world performance would be lower.

---

## Corporate Actions

The project relies on adjusted prices from Yahoo Finance.

Data quality depends on the provider.

---

# Future Work

Potential extensions include:

* Historical Nifty 100 constituent universe
* Sector-neutral portfolio construction
* Turnover optimization
* Transaction-cost modeling
* Risk-parity weighting
* Multi-factor integration
* Rolling factor decay analysis

---

# Technologies Used

* Python
* NumPy
* Pandas
* Matplotlib
* Yahoo Finance API

---

# Conclusion

This project demonstrates that a transparent factor-based portfolio construction framework using only historical information can generate substantial outperformance relative to both passive benchmarks and equal-weight allocations.

More importantly, the project highlights the importance of rigorous validation, bias detection, concentration analysis, and systematic portfolio construction.

The primary contribution is not the specific factor formula itself, but the research process used to develop, test, challenge, and validate the strategy.
