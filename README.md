# ICT Trading System

A research-oriented implementation of a deterministic ICT (Inner Circle Trader) detector for TradingView.

## Current status

**Phase 1 — ICT Detector, Version 1**

The current deliverable is an indicator, not a trading strategy. It detects the specified sequence:

`Liquidity Sweep → Displacement → MSS → FVG → Retracement → Micro MSS → Signal`

## Implemented concepts

- Confirmed swing highs and lows, with configurable swing length (default: 2)
- Latest confirmed swing used as the V1 liquidity simplification
- Strict same-candle SSL/BSL sweeps
- ATR-normalized bullish and bearish displacement
- Close-confirmed bullish and bearish MSS
- Three-candle bullish and bearish FVGs with configurable minimum size
- FVG association with the displacement/MSS event
- Retracement into the active FVG
- Confirmed micro MSS after retracement
- Optional chart visualizations for each detection group
- LONG and SHORT indicator signals

## Intentionally not implemented

This version does not place orders and does not use `strategy()`, `strategy.entry()`, or `strategy.exit()`. It has no broker/exchange connection, live trading, optimization, historical backtest engine, order blocks, OTE, premium/discount, kill zones, SMT, or higher-timeframe bias.

Future stop-loss/take-profit design is documented only: long SL below the SSL sweep extreme, short SL above the BSL sweep extreme, and targets at opposing liquidity.

## Using the Pine Script

1. Open `src/ict_detector_v1.pine`.
2. Copy the complete file.
3. Open TradingView's Pine Editor and paste the script.
4. Save it and click **Add to chart**.
5. Use the input groups to enable or disable visualizations and adjust parameters for research. Defaults are the V1 specification values; do not interpret parameter changes as optimization.

The script uses confirmed pivot logic. A pivot with length `N` is only available `N` bars after its reference candle. The marker may be drawn back on the reference candle, but detection and all dependent state changes occur on the confirmation bar.

## Roadmap

1. ICT Detector (current)
2. ICT Strategy
3. Historical backtesting
4. Parameter optimization
5. Out-of-sample validation
6. Walk-forward testing
7. Research/reporting

See `docs/strategy-spec.md`, `docs/development-log.md`, `docs/backtest-notes.md`, and `tests/test-cases.md` for traceability and manual validation.

## Disclaimer

This is a research and educational project. It is not financial advice and is not a guaranteed profitable trading system. Validate all behavior manually on TradingView before extending the project.
