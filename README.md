# Moving Average Crossover Backtest: AAPL

Python backtest comparing a 20/50-day moving average crossover strategy to buy-and-hold on AAPL, with realistic transaction costs, actual historical risk-free rates, and risk-adjusted performance metrics.

## What this is

This project tests a simple trend-following strategy against AAPL stock data from 2019 to 2026, comparing it to just buying and holding the stock. The strategy generates a buy signal when a short-term moving average crosses above a long-term moving average, and a sell signal when it crosses below.

## Result

| | CAGR | Max Drawdown | Sharpe |
|---|---|---|---|
| Buy & Hold | 31.06% | -33.36% | 0.94 |
| Strategy | 18.81% | -28.36% | 0.78 |

Buy-and-hold outperformed the strategy on both raw and risk-adjusted returns. The strategy made 18 round-trip trades over the period, spending about 65% of the time in the market. Transaction costs accounted for roughly 1.8% of cumulative return — verified directly in the notebook by comparing results with and without costs — so the underperformance is mainly about missed time in the market during recoveries, not trading fees.

Numbers reflect a run against live yfinance data on 09/02/2026. Re-running later may produce slightly different results if the underlying price data or T-bill rate series is revised.

## What I did

- Pulled AAPL daily price data and the 13-week T-bill rate from yfinance
- Calculated 20-day and 50-day moving averages and built buy/sell signals from their crossover
- Used the actual historical T-bill rate for idle cash instead of assuming 0% or a flat guess
- Charged a small transaction cost on every trade instead of assuming free trading
- Refactored the backtest into reusable functions (`max_drawdown`, `cagr`, `sharpe`, `summarize`)
- Added a sanity check comparing the implied cost drag against the expected drag from the trade count, to verify the transaction cost logic was actually correct rather than just trusting the output

## Assumptions and limitations

- Long/flat only — no short positions
- Full capital committed on every trade, no partial position sizing
- Fills happen at the closing price of the signal day, not the next day's open
- Single ticker, single parameter pair (20/50) — a multi-asset, multi-parameter robustness check is a planned follow-up
- Cash return while out of the market uses the actual historical T-bill rate where available, falling back to a flat 4% estimate if that data can't be pulled
- Data is pulled live from yfinance rather than cached, so results may shift slightly on re-run

## How to run

1. Clone this repo
2. Install dependencies: `pip install -r requirements.txt`
3. Open `Backtesting_v1.ipynb` and run all cells — this pulls AAPL price data and T-bill rates live from yfinance

## Tools

yfinance, pandas, NumPy, Matplotlib

## What's next

This is one strategy, one stock, one parameter pair — not enough to draw a strong general conclusion. The next step is testing the same strategy across a broad set of tickers and parameter combinations to check whether this result holds up, or whether it's specific to AAPL's exact price path over this window. This is planned as a direct follow-up while the codebase is still fresh, not an open-ended someday item.
