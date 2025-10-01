# Algorithmic Trading Strategy Backtest and Quantitative Analysis - Resume Project

This project serves as a practical demonstration of quantitative finance, algorithmic trading strategy development, rigorous backtesting, and statistical performance evaluation. It involves the end-to-end process of designing, implementing, and validating a data-driven trading strategy, with a strong focus on **money management**, **risk analysis**, and **simulated portfolio management**.

## Project Description

This project implements and backtests a quantitative trading strategy on a list of Indian stocks (Nifty 500). The strategy is based on a combination of technical indicators including Moving Averages (DMA 20 and 50), ADX, ATR, and On-Balance Volume (OBV). The backtest simulates trades over a specified historical period, managing capital and tracking performance metrics.

A key analytical component is the **A/B testing framework**, designed for a statistically sound comparison between the main trading strategy (Treatment Group) and a simpler Moving Average Crossover strategy (Control Group). This rigorous approach quantifies the value added by the main strategy's advanced filters and risk controls over a basic market timing technique.

## Trading Strategy

The trading strategy is a trend-following approach based on a combination of technical indicators and specific entry/exit rules designed to capture momentum while managing risk.

### Indicators Used

*   **DMA 20 & DMA 50**: Used to identify the short-term and medium-term trend direction and potential support/resistance levels.
*   **ADX (Average Directional Index)**: Measures the strength of the trend. A value above 25 typically indicates a strong trend.
*   **+DI and -DI**: Components of ADX used to determine the direction of the trend and momentum.
*   **ATR (Average True Range)**: Measures market volatility, used for calculating dynamic stop-loss levels and estimating risk per trade.
*   **OBV (On-Balance Volume)**: A cumulative volume indicator, used in conjunction with its moving average (OBV MA 20) to confirm trend strength by analyzing volume flow and potential accumulation/distribution.

### Entry Conditions

A long position is considered when:

*   The ADX is above 25 (indicating a strong trend).
*   The DMA 20 is above the DMA 50 (confirming an upward trend).
*   The closing price **touches the DMA 20** (within a predefined tolerance, `DMA_HIT_TOL`). This acts as a pullback entry signal within an established trend.
*   The OBV is above its 20-period moving average (confirming volume support for the upward price movement).
*   The +DI is above the -DI (confirming bullish momentum).
*   **Risk-Reward Filter**: The estimated potential reward (based on the distance to a hypothetical target) is at least 1.5 times the estimated potential risk (based on the ATR multiplier stop loss).
*   **Risk Percentage Filter**: The estimated risk per share, as a percentage of the entry price, is below a specified threshold (e.g., 10%). This prevents entering trades with excessively wide initial stop losses.

### Pyramid Entry (Add-on Position)

If a position is already open for a symbol, an additional position is considered as a "pyramid" or add-on when:

*   The ADX is above 25.
*   The DMA 20 is above the DMA 50 (trend remains upward).
*   The closing price **touches the DMA 50** (within a predefined tolerance, `DMA_HIT_TOL`), indicating a deeper pullback opportunity.
*   The OBV is above its 20-period moving average.
*   The +DI is above the -DI.
*   The same **Risk-Reward** and **Risk Percentage** filters apply as for the initial entry.

### Exit Conditions

Positions are exited under the following conditions:

*   **Dynamic Stop Loss**: The closing price falls below the Stop Loss level, which is dynamically calculated as **DMA 50 - (ATR * ATR\_MULTIPLIER)**. This trailing stop loss adjusts with volatility and the longer-term moving average.
*   **Trend Change**: The DMA 20 crosses below the DMA 50, indicating a potential trend reversal and prompting an exit to preserve capital.
*   **End of Backtest**: Any remaining open positions are closed at the final closing price of the backtest period.

### Risk Management and Money Allocation

*   **Position Sizing**: Position size is dynamically determined based on a percentage of the **current total portfolio capital** and the estimated risk per share for the trade. The formula used is `math.floor(RISK_PCT * current_capital / estimated_risk_per_share)`. This implements a **fixed fractional risk** model, a core concept in quantitative portfolio management.
*   **Risk per Trade**: Each trade is sized such that the potential loss, if the stop loss is hit, is limited to a predefined percentage (`RISK_PCT`) of the **current total portfolio capital**.
*   **Risk-Reward Filtering**: Entries are filtered based on a minimum required risk-reward ratio (`MIN_RISK_REWARD`), favoring trades with potentially higher returns relative to their risk.
*   **Maximum Open Positions**: The simulation limits the number of simultaneously open positions (`MAX_OPEN_POSITIONS`) to manage portfolio concentration risk.

## Methodology: The A/B Test and Portfolio Simulation

The project employs a rigorous methodology combining backtesting and statistical A/B testing to compare the implemented strategy against a baseline:

1.  **Control Group Strategy**: A simple Moving Average (DMA) Crossover strategy was implemented as the baseline for comparison. This strategy enters on a fast MA crossing above a slow MA and exits on the reverse crossover. Position sizing and capital management for the control group were handled consistently with the treatment group for a fair comparison.
2.  **Treatment Group Strategy**: The main trading strategy, incorporating DMA touch entries, ADX/OBV filters, ATR-based stop loss, DMA cross exits, and the detailed risk management rules, served as the treatment.
3.  **Consistent Simulation Environment**: Both strategies were simulated independently over the same historical period, starting with the same initial capital and operating within the same constraints (e.g., maximum open positions, though the MA crossover strategy typically opens fewer positions).
4.  **Performance Analysis**: Key performance indicators (KPIs) including **Total Return**, **Sharpe Ratio** (a measure of risk-adjusted return), **Win Rate**, and **Maximum Drawdown** were calculated for both simulated portfolios.
5.  **Statistical Inference**: **Hypothesis testing** using the T-test (for mean PnL) and Mann-Whitney U test (for PnL distribution) was conducted on the executed trades of both groups to statistically validate whether the performance difference between the two strategies was significant.

This methodology provides a robust, data-driven evaluation framework, crucial for assessing the efficacy of algorithmic trading strategies in a simulated **portfolio context** and demonstrating the impact of specific strategy components and risk controls.
