# Rules-Based Trading Strategy Backtester

A backtesting engine that implements and rigorously evaluates rules-based investment strategies against a buy-and-hold benchmark, across multiple stocks and sectors.

## Overview

This project builds and tests two classic rules-based trading strategies on 6 NSE-listed large-cap stocks (Jan 2021 – Jun 2026), comparing them against simple buy-and-hold on a risk-adjusted basis.

| Ticker | Sector |
|---|---|
| RELIANCE.NS | Energy / Conglomerate |
| TCS.NS | IT Services |
| INFY.NS | IT Services |
| HDFCBANK.NS | Banking |
| HINDUNILVR.NS | FMCG |
| BHARTIARTL.NS | Telecom |

## Strategies Tested

### 1. SMA Crossover (20/50)
A trend-following strategy. Goes long when the 20-day SMA crosses above the 50-day SMA ("golden cross") and exits when it crosses back below ("death cross").

### 2. RSI Mean-Reversion (30/70)
A mean-reversion strategy. Buys when the 14-day RSI drops below 30 (oversold), holds, and exits when RSI rises above 70 (overbought).

### 3. Buy & Hold (Benchmark)
Stays fully invested for the entire period — the baseline every active strategy must beat to justify its complexity.

## Methodology

- **No lookahead bias**: positions are based on the *previous* day's signal, never the current day's close
- **Long-only, no leverage**: strategies are either fully invested or fully in cash
- **No transaction costs modeled** (noted as a simplification — see Limitations)

### Performance Metrics Calculated
- **Total Return** and **CAGR** (annualized return)
- **Sharpe Ratio** (risk-adjusted return, 0% risk-free rate assumed)
- **Maximum Drawdown** (largest peak-to-trough decline)
- **Win Rate** (% of active trading days with positive return)

## Key Findings

**Strategy win count (best Sharpe ratio per stock, out of 6 stocks):**

| Strategy | Stocks Won |
|---|---|
| RSI Mean-Reversion | 3/6 |
| Buy & Hold | 2/6 |
| SMA Crossover | 1/6 |

**Average performance across all 6 stocks:**

| Strategy | Total Return % | CAGR % | Sharpe | Max Drawdown % | Win Rate % |
|---|---|---|---|---|---|
| Buy & Hold | 51.48 | 5.38 | 0.32 | -33.97 | 50.39 |
| RSI Mean-Reversion | 16.28 | 2.01 | 0.23 | -28.47 | 50.49 |
| SMA Crossover | 13.42 | 0.73 | 0.07 | -32.85 | 49.68 |

**Best risk-adjusted result**: BHARTIARTL.NS via Buy & Hold (Sharpe = 1.20, CAGR = 27.76%)

**Deepest drawdown**: INFY.NS via RSI Mean-Reversion (-55.53%)

### Takeaway

Buy & Hold outperformed both rules-based strategies **on average** across this basket, during a period that included a sustained multi-year uptrend. This is a well-documented, realistic pattern: timing strategies tend to underperform in strong trending markets because they exit during pullbacks and miss part of the subsequent recovery. However, RSI mean-reversion still won on 3 of 6 individual stocks (notably HDFCBANK), suggesting stock-specific behavior matters — the "best" strategy is not universal across a portfolio.

## Tech Stack

- **Python**: pandas, numpy, matplotlib
- **Data**: Reused from the companion ETL pipeline project (same processed OHLCV dataset)
- **Environment**: Google Colab

## How to Run

1. Run the companion ETL pipeline first (or load equivalent processed OHLCV data with `Daily_Return`, `SMA_20`, `SMA_50` columns)
2. Compute RSI and generate signals for both strategies
3. Run the backtest engine to compute equity curves
4. Compute performance metrics and generate visualizations

Outputs: `backtest_equity_curves.png`, `backtest_summary_report.txt`

## Limitations & Future Work

- **No transaction costs or slippage** — real-world returns for the active strategies would be lower, since SMA/RSI generate more trades than buy-and-hold
- **Fixed parameters** (20/50 for SMA, 30/70 for RSI) — a natural extension is parameter sensitivity testing (grid search) to check robustness
- **Market regime not isolated** — this backtest spans mostly a trending bull market; testing on a range-bound/sideways period could show different results for mean-reversion strategies
- **Single-asset strategies only** — could extend to portfolio-level position sizing and risk management (e.g. volatility targeting, stop-losses)
