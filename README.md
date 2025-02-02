# **Basic Momentum Strategy - A Play with Numbers**

In this repository, you'll find a **basic momentum strategy** implemented in Python. The idea is simple: track the top-performing stocks over the last 365 days and allocate capital to them. It’s not going to win any awards, but it’s a nice, straightforward way to understand some core concepts in quantitative finance, and—of course—play with numbers. The strategy is based on the concept of **momentum investing**, which aims to capitalize on the continuation of recent price trends.

### **How It Works**

This strategy uses a **momentum approach** where the top-performing stocks (based on their price movements over the last 365 days) are selected and held for the next period. Here’s a quick breakdown:

- **Stock Selection**: A list of global tickers (S&P 500, CAC40, Nikkei, etc.) is analyzed.
- **Data Source**: Stock data is fetched using the **Yahoo Finance API** (`yfinance` library).
- **Momentum Calculation**: Momentum is calculated using the percentage price change over the past year, smoothed over a 46-day period.
- **Transaction Costs**: A small fee (1%) is factored in for buying/selling stocks to keep things a little more realistic.

### **Steps Involved**

1. **Data Acquisition**: Stock price data (closing prices) for a set of tickers is downloaded using the `yfinance` API.
2. **Momentum Calculation**: The percentage change over the past 365 days is calculated, then smoothed over a 46-day window.
3. **Portfolio Construction**: Every day, the top 10 momentum stocks are selected and bought. If a stock drops out of the top 10, it is sold.
4. **Performance Tracking**: The strategy's performance is tracked and compared to the **S&P 500**. 

### **Key Parameters**

- **TOP_HELD = 10**: We hold the top 10 stocks with the highest momentum.
- **TRANSACTION_COST = 0.01**: A 1% transaction cost is applied to each buy/sell action.
- **MOMENTUM_TIMEFRAME = 365**: Momentum is based on the past year (365 days).
- **SMOOTHED_CHANGE_PERIOD = 46**: Momentum changes are smoothed over a 46-day period.

### **Performance Analysis**

While this strategy is simple and can be a fun way to experiment with basic concepts, the **Sharpe Ratio** of the strategy is a reminder that simplicity has its limits. In fact, the Sharpe Ratio of this strategy tends to hover around **0.1**—which, frankly, isn't great. But that’s kind of the point: we’re here to explore and understand, not necessarily to make fortune-changing returns.

You can expect the portfolio to underperform compared to **S&P 500** in most cases, with some small periods of outperformance. Still, it’s a useful exercise to see how the strategy works in real-time, and to experiment with modifying key parameters (like the number of stocks held, the smoothing window, or the transaction costs) to see how they impact performance.

### **Next Steps**

If you want to extend or improve this strategy:
- **Add More Indicators**: You could incorporate other technical indicators like **RSI** (Relative Strength Index) or **MACD** to enhance the strategy.
- **Implement Risk Management**: Introduce stop-loss mechanisms, volatility-based weighting, or dynamic position sizing to better manage risk.
- **Use More Sophisticated Backtesting Tools**: For a more robust and scalable backtesting environment, consider using platforms like **Backtrader** or **QuantConnect**.

### **Key Results**

The performance of this strategy, when plotted, can be compared with the **S&P 500** for insight into its relative performance. If you’re into **quantitative analysis**, the Sharpe Ratio provides a good measure of risk-adjusted return. In this case, it’s pretty low—so, no need to quit your day job just yet.

### **Dependencies**

To run the strategy, you'll need the following Python libraries:
- `yfinance` for fetching financial data from Yahoo Finance.
- `pandas` for handling the data.
- `matplotlib` for plotting the results.
- `joblib` for parallel processing.

---

Conclusion

In the world of quantitative finance, this is as basic as it gets. The goal of this repository isn’t to offer a revolutionary strategy—it’s simply to explore momentum and play with data. So, if you're looking for something to bring instant riches, this is definitely not it. But if you're interested in learning how strategies are built and tested, this is a good place to start.

### **Example Code**

Here’s a snippet of the actual implementation, which you can run directly in your Python environment:

```python
import yfinance as yf
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from joblib import Parallel, delayed

# Define strategy parameters
TOP_HELD = 10
TRANSACTION_COST = 0.01
MOMENTUM_TIMEFRAME = 365
SMOOTHED_CHANGE_PERIOD = 46
TICKERS = ['AAPL', 'GOOG', 'AMZN', 'MSFT', 'TSLA', 'FB', 'NVDA', 'SPY', 'SP500', 'DJIA']  # Example tickers

# Fetch data
def fetch_data(ticker):
    data = yf.download(ticker, period="5y", interval="1d")
    return data['Close']

def calculate_momentum(data):
    # Calculate momentum based on the percentage change over 365 days
    momentum = (data.pct_change(periods=MOMENTUM_TIMEFRAME) * 100).rolling(SMOOTHED_CHANGE_PERIOD).mean()
    return momentum

# Performance tracking
def track_performance(tickers):
    data = Parallel(n_jobs=-1)(delayed(fetch_data)(ticker) for ticker in tickers)
    momentum_data = pd.DataFrame({ticker: calculate_momentum(d) for ticker, d in zip(tickers, data)})
    # Portfolio construction logic goes here...
    # Calculate returns and Sharpe Ratio...
    return momentum_data

# Call the performance tracking function
momentum_data = track_performance(TICKERS)
momentum_data.plot()
plt.show()

