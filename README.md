# SMC / ICT Pro — Smart Money Concepts Indicator
> **Pine Script v5 · TradingView · Full SMC + ICT Methodology**

A professional-grade, fully automated Smart Money Concepts indicator for TradingView built on ICT (Inner Circle Trader) principles. Detects market structure, liquidity sweeps, order blocks, fair value gaps, and generates complete trade setups with entry, stop loss, and three take-profit levels — all visualized with a clean Multi-Timeframe Dashboard.

---

## Table of Contents

- [Features Overview](#features-overview)
- [Installation](#installation)
- [Input Reference](#input-reference)
- [How It Works](#how-it-works)
  - [Market Structure Engine](#1-market-structure-engine)
  - [Liquidity Sweeps](#2-liquidity-sweeps)
  - [Order Blocks](#3-order-blocks)
  - [Fair Value Gaps](#4-fair-value-gaps)
  - [Trade Execution Logic](#5-trade-execution-logic)
  - [EMA Ribbon & Trail Exit](#6-ema-ribbon--trail-exit)
  - [MTF Dashboard](#7-mtf-dashboard)
- [Alert Conditions](#alert-conditions)
- [Recommended Settings by Asset](#recommended-settings-by-asset)
- [Visual Guide](#visual-guide)
- [FAQ](#faq)
- [License](#license)

---

## Features Overview

| Module | Description |
|--------|-------------|
| 🏗 **Swing Pivots** | Auto-detects Swing Highs and Swing Lows with configurable left/right pivot bars |
| 📐 **BOS / CHoCH** | Plots Break of Structure and Change of Character on confirmed candle-body closes |
| ⚡ **Liquidity Sweeps** | Identifies stop hunts (wick-outside, body-inside reversals) at key structural levels |
| 🟩 **Order Blocks** | Marks the last opposing candle before a structural break as a supply/demand zone |
| 🟦 **Fair Value Gaps** | Highlights 3-candle price imbalances (gaps between candle[2] high and candle[0] low) |
| 🎯 **Trade Setups** | Generates BUY RE / SELL RE labels with Entry, SL, TP1, TP2, and TP3 levels |
| 📦 **R:R Boxes** | Color-coded risk/reward boxes drawn instantly at signal confirmation |
| 📉 **EMA Ribbon** | Fast/Slow EMA cloud for trend-following context and trailing exit signals |
| ⚠️ **EMA Trail Exit** | Prints an exit label when price crosses back through the EMA ribbon |
| 📊 **MTF Dashboard** | Top-right table showing Bias + ADX + Signal strength across 4 timeframes |
| 🔔 **8 Alert Conditions** | Pre-built alerts for every key signal — ready for webhooks or notifications |

---

## Installation

1. Open [TradingView](https://www.tradingview.com) and load any chart.
2. Click the **Pine Script Editor** tab at the bottom of the screen.
3. Delete all existing code in the editor.
4. Open `SMC_ICT_Pro.pine` from this repository and **copy the entire contents**.
5. Paste the code into the Pine Script Editor.
6. Click **Save** (give it a name like `SMC ICT Pro`).
7. Click **Add to chart**.

The indicator will load directly on your chart with all default settings active.

> **Tip:** Right-click the indicator name on the chart and select **Settings** to open the full input panel.

---

## Input Reference

### ▸ Market Structure

| Input | Default | Description |
|-------|---------|-------------|
| `Pivot Left Bars` | `5` | Number of bars to the left used to confirm a swing pivot |
| `Pivot Right Bars` | `5` | Number of bars to the right used to confirm a swing pivot |
| `Show BOS Labels` | `true` | Toggle Break of Structure line and label visibility |
| `Show CHoCH Labels` | `true` | Toggle Change of Character line and label visibility |
| `Show Liquidity Sweeps` | `true` | Toggle the `SWEEP ⚡` label on stop-hunt candles |
| `Show Order Blocks` | `true` | Toggle order block zone boxes |
| `Show Fair Value Gaps` | `true` | Toggle FVG imbalance boxes |

### ▸ EMA Ribbon & Trail Exit

| Input | Default | Description |
|-------|---------|-------------|
| `EMA Fast` | `21` | Period for the fast EMA line |
| `EMA Slow` | `50` | Period for the slow EMA line |
| `Show EMA Ribbon Cloud` | `true` | Toggle the filled EMA cloud on the chart |
| `Show EMA Trail Exit Label` | `true` | Toggle the `⚠ EMA EXIT` label on ribbon crossovers |

### ▸ Trade Execution

| Input | Default | Description |
|-------|---------|-------------|
| `SL Buffer (pips/points)` | `1.5` | Extra distance added beyond the sweep wick for the Stop Loss |
| `TP1 R:R Ratio` | `1.5` | Risk-to-reward multiplier for the first take-profit target |
| `Show R:R Box` | `true` | Toggle the visual risk/reward box drawn at each setup |
| `Max Concurrent Setups` | `3` | Visual cap on simultaneous setup boxes (display only) |

### ▸ MTF Dashboard

| Input | Default | Description |
|-------|---------|-------------|
| `Show MTF Dashboard` | `true` | Toggle the entire dashboard table |
| `TF 1` | `5` | First timeframe (minutes) shown in the dashboard |
| `TF 2` | `15` | Second timeframe (minutes) |
| `TF 3` | `60` | Third timeframe (1 Hour) |
| `TF 4` | `240` | Fourth timeframe (4 Hour) |
| `ADX Length` | `14` | Lookback period for the ADX calculation |
| `ADX Trend Threshold` | `25.0` | ADX value above which a market is considered trending |

---

## How It Works

### 1. Market Structure Engine

Swing Highs and Swing Lows are identified using `ta.pivothigh` and `ta.pivotlow` with the configurable left/right bar inputs. The indicator stores the most recent 6 confirmed pivots in arrays and uses them for all downstream logic.

**Break of Structure (BOS):**
Fires when a candle *body* closes beyond the most recent swing level *in the same direction* as the existing bias. This confirms trend continuation and is drawn as a **dashed line**.

**Change of Character (CHoCH):**
Fires when a candle body closes beyond the most recent swing level *against* the existing bias. This signals a potential trend reversal and is drawn as a **solid, thicker line**. CHoCH is the higher-priority signal.

```
Bearish Bias → Price closes above last Swing High → CHoCH ▲ (Bullish Shift)
Bullish Bias → Price closes below last Swing Low  → CHoCH ▼ (Bearish Shift)
```

A subtle background tint (green/red) reflects the current internal market bias at all times.

---

### 2. Liquidity Sweeps

A liquidity sweep (stop hunt) is detected when:

- The **wick** of a candle breaks beyond a key Swing High or Swing Low
- But the candle **body closes back inside** the previous range
- Indicating smart money hunted stops then immediately reversed

```
Bullish Sweep:  low < lastSwingLow  AND  close > lastSwingLow  AND  open > lastSwingLow
Bearish Sweep:  high > lastSwingHigh AND  close < lastSwingHigh AND  open < lastSwingHigh
```

A `SWEEP ⚡` label is printed at the wick extreme when detected.

---

### 3. Order Blocks

An Order Block is the **last opposing candle** before a significant structural break (BOS or CHoCH). It represents the final point where institutional orders were placed before the move.

- **Bullish OB:** The last bearish candle body before a bullish BOS/CHoCH — drawn as a green transparent box.
- **Bearish OB:** The last bullish candle body before a bearish BOS/CHoCH — drawn as a red transparent box.

Up to 3 of each type are tracked at a time. Older boxes are automatically removed when new ones form. OB levels are used as TP2 targets in trade setups.

---

### 4. Fair Value Gaps

A Fair Value Gap (FVG) is a 3-candle imbalance where there is no overlap between the first and third candle's wicks, creating a price gap that acts as a magnet for future price action.

```
Bullish FVG: candle[0] low > candle[2] high  (gap upward)
Bearish FVG: candle[0] high < candle[2] low  (gap downward)
```

FVGs are drawn as dotted transparent boxes and extend 20 bars to the right.

---

### 5. Trade Execution Logic

A full trade setup is generated **only when two conditions align simultaneously:**

1. A **Liquidity Sweep** has just occurred at a key structural level
2. The following candle provides **confirmation** via an engulfing close or a strong rejection body (body > 50% of the candle range)

#### 🟢 BUY RE — Long Setup

Triggered after a **bullish liquidity sweep** (sweep of a Swing Low) with bullish confirmation.

| Level | Calculation |
|-------|-------------|
| **Entry** | Close of the confirmation candle |
| **Stop Loss** | `Sweep candle low − SL Buffer` |
| **TP1** | `Entry + (Risk × TP1 R:R ratio)` — default 1:1.5 |
| **TP2** | Nearest **bearish Order Block** above entry (falls back to `Risk × 2.0` if none) |
| **TP3** | Most recent major **Swing High** (the opposite liquidity pool) |

#### 🔴 SELL RE — Short Setup

Triggered after a **bearish liquidity sweep** (sweep of a Swing High) with bearish confirmation.

| Level | Calculation |
|-------|-------------|
| **Entry** | Close of the confirmation candle |
| **Stop Loss** | `Sweep candle high + SL Buffer` |
| **TP1** | `Entry − (Risk × TP1 R:R ratio)` — default 1:1.5 |
| **TP2** | Nearest **bullish Order Block** below entry (falls back to `Risk × 2.0` if none) |
| **TP3** | Most recent major **Swing Low** (the opposite liquidity pool) |

Each setup renders:
- A multi-part **color-coded R:R box** (red = risk zone, green = TP1, teal = TP2, purple = TP3)
- **Horizontal dashed lines** at every level
- A **multi-line label** showing all price levels in one glance

---

### 6. EMA Ribbon & Trail Exit

The EMA Ribbon uses a fast and slow EMA to create a visual trend cloud:

- **Green cloud** — Fast EMA above Slow EMA (bullish momentum)
- **Red cloud** — Fast EMA below Slow EMA (bearish momentum)

**EMA Trail Exit** triggers when:
- A long trade is running and price crosses and **closes below the Slow EMA** → prints `⚠ EMA EXIT` above the candle
- A short trade is running and price crosses and **closes above the Slow EMA** → prints `⚠ EMA EXIT` below the candle

This helps secure open profits before a full structural reversal develops.

---

### 7. MTF Dashboard

A fixed table in the **top-right corner** of the chart displays market context across four timeframes simultaneously.

| Column | Content |
|--------|---------|
| **TF** | The timeframe label (e.g. 5, 15, 60, 240) |
| **Bias** | `▲ BULL` / `▼ BEAR` / `◆ NEUT` based on EMA ribbon direction on that TF |
| **ADX** | Current ADX value with a `✦` if above threshold (trending) or `·` if below (ranging) |
| **Trend** | `TREND` (green) or `RANGE` (grey) |
| **Signal** | `🟢 GO` (bullish + trending), `🔴 GO` (bearish + trending), or `⏸ WAIT` (ranging) |

The dashboard updates in real time on every bar close.

---

## Alert Conditions

The indicator includes **8 pre-built alert conditions**. To set one up: right-click the indicator → **Add Alert**.

| Alert Name | Trigger |
|------------|---------|
| `SMC Long Setup (BUY RE)` | Sweep + bullish confirmation candle |
| `SMC Short Setup (SELL RE)` | Sweep + bearish confirmation candle |
| `Bullish Liquidity Sweep` | Any bullish stop hunt detected |
| `Bearish Liquidity Sweep` | Any bearish stop hunt detected |
| `CHoCH — Bullish Shift` | Market structure flips bullish |
| `CHoCH — Bearish Shift` | Market structure flips bearish |
| `EMA Trail Exit — Long` | Price closes below Slow EMA while bullish |
| `EMA Trail Exit — Short` | Price closes above Slow EMA while bearish |

> All alerts support TradingView webhooks for integration with trading bots or notification services.

---

## Recommended Settings by Asset

| Asset Class | Pivot L/R | SL Buffer | EMA Fast/Slow | Notes |
|-------------|-----------|-----------|---------------|-------|
| **Forex Majors** | 5 / 5 | 1.0–1.5 | 21 / 50 | Default settings work well |
| **Crypto (BTC/ETH)** | 5 / 5 | 3.0–5.0 | 21 / 50 | Wider SL for high volatility |
| **US Indices (SPX, NAS)** | 4 / 4 | 2.0–3.0 | 13 / 34 | Tighter pivots for faster structure |
| **Gold (XAUUSD)** | 5 / 5 | 2.0–3.0 | 21 / 50 | Standard with wider buffer |
| **Stocks** | 6 / 6 | 1.5–2.5 | 21 / 50 | Looser pivots for daily charts |

**Recommended timeframe combinations for the MTF Dashboard:**

| Trading Style | TF1 | TF2 | TF3 | TF4 |
|---------------|-----|-----|-----|-----|
| Scalping | 1m | 5m | 15m | 1H |
| Day Trading | 5m | 15m | 1H | 4H |
| Swing Trading | 1H | 4H | 1D | 1W |

---

## Visual Guide

```
Chart Elements at a Glance
───────────────────────────────────────────────────────

  ── ── ── ──  CHoCH ▲          Thick solid line (green) = structure flip
  - - - - - -  BOS              Dashed line = continuation break

  [████████]   Bullish OB       Green transparent box = demand zone
  [████████]   Bearish OB       Red transparent box = supply zone
  [░░░░░░░░]   FVG              Dotted box = price imbalance gap

  SWEEP ⚡      (orange label)  Stop hunt / liquidity grab detected

  ┌──────────────────────┐
  │  🔴 SELL RE           │  ← Entry label with all levels listed
  │  Entry: 1.2345        │
  │  SL:    1.2360        │
  │  TP1:   1.2320        │
  │  TP2:   1.2290        │
  │  TP3:   1.2250        │
  └──────────────────────┘

  [  Purple box  ]  TP3 zone
  [  Teal box    ]  TP2 zone
  [  Green box   ]  TP1 zone
  [  Red box     ]  Risk zone (Entry → SL)

  ⚠ EMA EXIT    (orange label)  Trail exit — close position

  ╔═══════════════════════════════════╗
  ║      SMC·ICT PRO                  ║  ← Top-right dashboard
  ║  TF   Bias    ADX   Trend  Signal ║
  ║   5   ▲ BULL  28.4✦ TREND 🟢 GO  ║
  ║  15   ▼ BEAR  19.1·  RANGE ⏸ WAIT║
  ║  60   ▲ BULL  31.2✦ TREND 🟢 GO  ║
  ║ 240   ▲ BULL  27.8✦ TREND 🟢 GO  ║
  ╚═══════════════════════════════════╝
```

---

## FAQ

**Q: Why isn't the BUY/SELL RE label appearing even though I can see a sweep?**
A: The setup requires *both* a liquidity sweep AND a confirmed engulfing/rejection candle. If the confirmation candle is weak (body < 50% of range), no label is generated. This is intentional — it filters low-quality signals.

**Q: The TP2 level seems too close/far. How do I control it?**
A: TP2 is dynamically placed at the nearest Order Block in the trade direction. If no OB exists, it falls back to a `2.0 × Risk` target. Enable `Show Order Blocks` to see which OB is being used.

**Q: Can I use this on non-forex markets like crypto or stocks?**
A: Yes. Adjust the `SL Buffer` upward for higher-volatility markets. For crypto, `3.0–5.0` is recommended. The pip unit is `syminfo.mintick` so it auto-adapts to any instrument.

**Q: The MTF Dashboard shows `⏸ WAIT` on all timeframes. Is something wrong?**
A: No — this means the ADX is below your threshold on those timeframes, indicating ranging conditions. This is a valid filter. Consider lowering the ADX Threshold to `20` if you want signals in lower-volatility environments.

**Q: How do I turn off features I don't want?**
A: Every major visual element has a dedicated toggle in the Settings panel (Show BOS, Show CHoCH, Show Sweeps, Show OB, Show FVG, Show EMA Cloud, Show Dashboard, Show R:R Box). You can disable any combination.

**Q: Can I set up a bot to trade these signals automatically?**
A: Yes — use TradingView's **Webhook Alerts**. Each of the 8 alert conditions can trigger a webhook to any compatible broker API or trading bot platform.

---

## License

This indicator is released for **personal and educational use**. You may modify it freely for your own trading. Redistribution or resale of this code, in original or modified form, is not permitted without explicit written permission.

> **Disclaimer:** This indicator is a tool to assist analysis — it does not guarantee profitable trades. All trading involves risk. Always use proper risk management and never risk more than you can afford to lose.
