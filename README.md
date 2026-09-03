# Moving Average Crossover Backtest

Python backtest comparing a moving average crossover strategy to buy and hold, with risk adjusted performance metrics.

## What this is

This project backtests a simple moving average crossover trading strategy against AAPL stock data from 2019 to 2026, and compares it to a basic buy and hold approach using both raw returns and risk adjusted metrics (CAGR, max drawdown, Sharpe ratio).

The strategy generates a buy signal when a short-term moving average crosses above a long-term moving average, and a sell signal when it crosses below, one of the most common introductory strategies in quantitative finance.

## What I did

- Pulled AAPL daily price data using yfinance
- Calculated a 20day and 50day moving average
- Built buy/sell signals based on when the two averages cross
- Accounted for idle cash earning a risk-free rate while out of the market, rather than assuming 0%
- Compared cumulative returns, CAGR, Sharpe ratio, and max drawdown between the strategy and buy and hold
- Plotted price with both moving averages overlaid, and drawdown over time, to make the strategy's behavior visible rather than just summarized in numbers

## Result

Buy and hold clearly won here, both on raw returns and once you adjust for risk. The strategy did lose less during downturns, but not enough to make up for how much growth it missed out on. Kind of expected, since simple moving average strategies tend to get whipsawed out of the market and miss the sharp parts of a recovery, and that really adds up over a strong bull run like AAPL's had.

| | CAGR | Max Drawdown |
|---|---|---|
| Buy & Hold | ~32.7% | -33.4% |
| Strategy | ~17.4% | -29.1% |

(Sharpe ratios are in the notebook itself.)

## Assumptions and limitations

- No transaction costs or slippage
- Long/flat only, no short positions
- Full capital committed on every trade, no partial position sizing
- Single ticker (AAPL) and single parameter pair (20/50),  not yet tested for robustness across other stocks or window lengths
- Cash return while out of the market is approximated at a flat rate, not the actual historical risk-free rate at each point in time

## Tools

yfinance, pandas, NumPy, Matplotlib

## Development notes

Ran into a sneaky bug early on, yfinance returns price data with a weird multi-index column structure, and my derived columns (like the signal) didn't line up with it the same way. If I'd written the returns calculation as a simple one-liner instead of building it column by column, it would've silently returned all NaN values with zero error message. Fixed it by flattening the columns right after downloading the data. Also fixed how idle cash was treated — originally it just earned 0%, which isn't realistic since cash actually earns some return sitting in a money market or T-bill.

## What's next

Right now this is one strategy, one stock, one set of parameters, not really enough to say anything solid. Next step is running the same strategy across a bunch of different tickers and parameter combinations to see if this result actually holds up, or if it's just specific to how AAPL happened to move during this exact stretch.
