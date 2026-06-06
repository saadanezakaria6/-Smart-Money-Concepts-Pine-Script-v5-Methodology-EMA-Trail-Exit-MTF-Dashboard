
Here's your complete SMC / ICT Pro indicator. Here's a full breakdown of what's inside and how to use it:
Feature Map
#
Module
What it does
1
Swing Pivots
Detects highs/lows via ta.pivothigh/pivotlow with adjustable left/right bars. Stores last 6 in arrays.
2
BOS / CHoCH
Fires on candle-body close past the last confirmed swing. CHoCH = counter-trend (thick line), BOS = continuation (dashed).
3
Liquidity Sweeps
Wick-outside + body-inside logic for both bull and bear sweeps. Prints SWEEP ⚡ label.
4
Order Blocks
Last opposing candle before a BOS/CHoCH move. Drawn as transparent boxes with labels, auto-expiring.
5
Fair Value Gaps
3-candle gap detection (true imbalance between candle[2] and candle[0]). Drawn as dotted boxes.
6
EMA Ribbon
Fast/Slow EMA with filled cloud (green=bull, red=bear). Fully toggleable.
7
BUY/SELL RE Labels
Fires only on Sweep + Engulfing/rejection confirmation. Plots Entry, SL, TP1/2/3 lines and color-coded R:R boxes.
8
EMA Trail Exit
Prints ⚠️ EMA EXIT when price crosses back through the slow EMA while in a trade direction.
9
MTF Dashboard
Top-right table showing Bias + ADX + Signal for 4 configurable timeframes. Green ✦ = strong trend, grey dot = ranging.
10
Alerts
8 pre-built alert conditions for all key signals.
How to Install
Open TradingView → Pine Script Editor (bottom panel)
Delete any existing code, paste the entire script
Click Save → then Add to chart
Key Inputs to Tune
Input
Suggested Range
Effect
Pivot Left/Right
3–8
Tighter = more signals, looser = cleaner structure
EMA Fast/Slow
21/50 or 8/21
Ribbon sensitivity
SL Buffer (pips)
1.0–3.0
Adjust for instrument volatility (tighter on crypto, wider on FX)
TP1 R:R
1.5–2.0
Partial profit target multiplier
ADX Threshold
20–30
Below 25 = ranging market filter
Pro tip: On higher-volatility assets (crypto), increase the SL Buffer to 3.0–5.0 pips to avoid noise stops. On Forex majors, 1.0–2.0 is ideal.
