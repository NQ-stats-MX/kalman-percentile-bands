# kalman-percentile-bands
A mean-reversion indicator for TradingView (Pine Script v6). It plots a volatility-adaptive Kalman mean with three empirical percentile bands on each side.

Built for intraday scalping (tested on NQ, 1-minute), but it works on any symbol and timeframe.

## Why percentiles instead of standard deviations?

Bollinger-style bands assume price deviations follow a normal distribution. Markets don't: they have fat tails and are often skewed. KPB measures the **actual distribution** of deviations, so a "95% band" really contains about 95% of the observed price action.

## How it works

1. **Kalman mean.** A local-level Kalman filter tracks the fair value of price. In volatility-adaptive mode it speeds up when short-term vol exceeds long-term vol and calms down in quiet markets.
2. **Vol-normalized deviation.** The distance from the mean is divided by an EWMA of 1-bar volatility: `z = (price − mean) / vol`.
3. **Percentile bands.** The script takes the empirical percentiles of `z` over a lookback window (1000 bars by default) and scales them by current vol: `band = mean + percentile(z) × vol`.

The result is bands that respect real tail behavior and still widen or tighten immediately when volatility changes.

## Features

- Three upper and three lower bands with adjustable percentiles (default 80 / 95 / 99)
- Two percentile modes:
  - **Central:** 95 means 95% of bars fall inside the band pair, with 2.5% beyond each side
  - **One-sided:** 95 means the 95th percentile up and the 5th percentile down
- An option to make the bands ignore the current bar, so a spike can't widen its own band
- Full styling: color, opacity, width and line style for every band and the mean
- Six independent zone fills
- Optional background highlight when price closes beyond a chosen band
- Price labels on the last bar
- A readout table with the current percentile rank, z, and the speed of the mean

## Installation

1. In TradingView, open the **Pine Editor**
2. Paste the contents of `Kalman_Percentile_Bands.pine`
3. Click **Add to chart**

## Key settings

| Setting | Default | What it does |
|---|---|---|
| Q/R sensitivity | 0.02 | How fast the mean follows price |
| Volatility-adaptive mean | On | Mean speeds up in high-vol bursts |
| Band volatility length | 30 | How quickly the bands react to vol |
| Percentile lookback | 1000 | History used for the percentiles; keep it high for stable 99% bands |
| Percentile mode | Central | Two-sided or one-sided interpretation |

## Notes

- The 99% band relies on rare observations, so it needs a long lookback to be stable.
- This is an analysis tool, not a trading system. It produces no buy or sell signals.
- Not financial advice. Test before you trade.
