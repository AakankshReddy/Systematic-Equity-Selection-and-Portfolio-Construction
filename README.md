# Factor-Based Portfolio Construction on Indian Equities

## Overview

This project develops and evaluates a systematic factor-investing strategy for Indian equities using historical market data.

The objective is to determine whether a rules-based portfolio constructed using momentum and risk-adjusted factors can outperform both:

- Equal-weight portfolios
- Nifty 50 benchmark

The strategy is evaluated using a fully out-of-sample walk-forward backtesting framework spanning more than a decade of market history.

---

# Motivation

Most retail investors rely on discretionary stock selection.

Factor investing instead attempts to identify characteristics that have historically been associated with long-term outperformance and construct portfolios using quantitative rules.

This project investigates whether a combination of:

- Momentum
- Low Volatility

can generate superior risk-adjusted returns in Indian large-cap equities.

---

# Dataset

## Source

Yahoo Finance (`yfinance`)

## Period

January 2010 – January 2024

## Universe

Large-cap Indian equities with long historical records and strong liquidity.

Examples include:

- HDFCBANK
- ICICIBANK
- RELIANCE
- TCS
- INFY
- TITAN
- BAJFINANCE
- BRITANNIA
- MARUTI
- ASIANPAINT

## Data Cleaning

The following preprocessing steps were performed:

- Removal of completely empty tickers
- Elimination of stocks with insufficient history
- Daily return calculation
- Walk-forward training/testing split

---

# Strategy Design

## Factor 1: Momentum

Measures cumulative performance over a lookback window.

Stocks with stronger historical returns receive higher scores.

---

## Factor 2:  Low Volatility

Annualized volatility is computed from daily returns.

Lower-volatility stocks receive higher scores.

---

# Composite Score

Each factor is ranked cross-sectionally.

Final score:

```text
Momentum Rank
×
Low Volatility Rank
```

Higher scores indicate stronger candidates.

---

# Portfolio Construction

At each rebalance:

1. Compute factor scores using historical data only
2. Select Top 15 stocks
3. Apply inverse-volatility weighting
4. Cap individual positions at 35%
5. Rebalance every 21 trading days

No future information is used during portfolio construction.

---

# Backtesting Framework

The strategy is evaluated using a rolling walk-forward process:

```text
Training Window
        ↓
Factor Computation
        ↓
Portfolio Construction
        ↓
Future Return Realization
        ↓
Rebalance
```

This process is repeated throughout the sample period.

---

# Lookback Robustness Testing

Multiple lookback windows were tested:

| Lookback | CAGR | Sharpe | Max Drawdown |
|-----------|--------:|--------:|--------:|
| 126 Days | 20.24% | 0.84 | -38.68% |
| 252 Days | 24.84% | 1.11 | -33.21% |
| 315 Days | 24.66% | 1.12 | -33.22% |
| 504 Days | 25.58% | 1.16 | -31.88% |
| 756 Days | 24.08% | 1.06 | -33.42% |

## Observation

Performance improves significantly for medium and long-horizon lookbacks.

The strategy remains profitable and stable across all windows from 252–756 trading days, suggesting robustness rather than parameter overfitting.

---

# Benchmark Comparison

## Best Configuration (504-Day Lookback)

| Metric | Strategy | Equal Weight | Nifty 50 |
|----------|---------:|---------:|---------:|
| CAGR | 25.58% | 21.81% | 13.21% |
| Sharpe Ratio | 1.16 | 1.02 | 0.43 |
| Max Drawdown | -31.88% | -35.34% | -38.44% |
| Total Return | 1143.59% | 787.29% | 294.83% |

## Key Result

The strategy:

- Outperformed the Nifty 50 benchmark
- Outperformed the equal-weight portfolio
- Delivered higher risk-adjusted returns
- Experienced lower drawdowns than the market benchmark

---

# Turnover Analysis

Average monthly turnover:

```text
12.3%
```

Turnover Statistics:

| Metric | Value |
|----------|--------:|
| Mean | 12.30% |
| Median | 11.71% |
| Maximum | 39.25% |
| Minimum | 2.28% |

## Observation

Portfolio turnover remained relatively low despite monthly rebalancing, indicating that the strategy tends to hold winners rather than aggressively rotate positions.

---

# Transaction Cost Analysis

Performance under realistic transaction costs:

| Cost per Trade | CAGR |
|---------------|--------:|
| 10 bps | 23.90% |
| 25 bps | 23.64% |
| 50 bps | 23.19% |

## Observation

The strategy remains profitable even under relatively high transaction cost assumptions.

This is largely due to low portfolio turnover.

---

# Portfolio Characteristics

## Most Frequently Selected Stocks

| Stock | Number of Selections |
|---------|---------:|
| BAJFINANCE | 99 |
| PIDILITIND | 87 |
| BERGEPAINT | 82 |
| BRITANNIA | 81 |
| TITAN | 62 |
| BAJAJFINSV | 60 |
| HAVELLS | 57 |
| HCLTECH | 56 |
| BEL | 53 |
| EICHERMOT | 52 |

## Observation

The factor model repeatedly selected companies known for:

- Strong earnings growth
- Consistent business performance
- Durable competitive advantages

This serves as a qualitative validation of the factor framework.

---

# Sector Exposure

Average sector exposure:

| Sector | Weight |
|----------|--------:|
| Financials | 11.3% |
| FMCG | 11.1% |
| IT | 10.8% |
| Consumer | 10.5% |
| Auto | 7.0% |
| Metals | 4.8% |
| Industrials | 4.2% |
| Pharma | 3.8% |
| Energy | 2.9% |
| Utilities | 0.4% |

The remaining weight belongs to stocks not explicitly mapped in the sector classification.

## Observation

The strategy is not dominated by a single sector and exhibits broad diversification across multiple industries.

---

# Key Findings

1. Momentum signals become substantially stronger when measured over longer horizons.

2. Lookbacks between 252 and 756 trading days consistently outperformed both benchmarks.

3. The best-performing configuration achieved:

```text
25.58% CAGR
1.16 Sharpe Ratio
-31.88% Maximum Drawdown
```

4. The strategy maintained performance after incorporating realistic transaction costs.

5. Portfolio turnover remained modest at approximately 12% per rebalance.

6. The model naturally concentrated in high-quality compounders such as:

- Bajaj Finance
- Titan
- Britannia
- Berger Paints
- Pidilite

---

# Limitations

- Yahoo Finance data quality limitations
- Potential survivorship bias in universe construction
- Historical index constituent changes not fully incorporated
- No sector-neutral constraints
- No explicit optimization for tax efficiency
- Corporate actions rely on Yahoo-adjusted prices

---

# Future Work

Potential extensions include:

- Survivorship-bias-free universes
- Risk parity portfolio construction
- Sector-neutral factor portfolios
- Multi-factor attribution analysis
- Machine learning ranking models
- Alternative data sources
- Fundamental factor integration

---

# Technologies Used

- Python
- Pandas
- NumPy
- Matplotlib
- yfinance

---

# Conclusion

A systematic factor-investing framework based on momentum and low volatility successfully outperformed both equal-weight and market benchmarks over a 10+ year period.

The strategy demonstrated:

- Robustness across lookback windows
- Low turnover
- Resilience to transaction costs
- Consistent stock selection behavior

These results suggest that medium-to-long-term momentum signals can be effectively exploited in Indian large-cap equities using a transparent and fully rules-based investment process.