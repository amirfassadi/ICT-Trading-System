# V1 research backtest protocol

This is an experimental wrapper, not an accepted live strategy. Requested market: BINANCE:BTCUSDT, standard 1-hour candles. Detector baseline: 2911242ab01c0d8d89cd2a027d611d8a91467782; source blob 2b07b212d86defb474dd902b52954260e1f32943.

## Fixed assumptions chosen before reading performance
- Entry signal timestamps: 2026-07-21 00:00 UTC through the bar before 2026-10-19 00:00 UTC. The final bar whose close equals end is excluded from new entry submission to prevent entry after the window.
- All loaded earlier bars warm up the original V1 detector.
- Retain V1 detection and directional state transitions unchanged.
- Market entry on the next available tick, normally next bar open; no on-close fills for entries.
- Fixed stop one minimum tick below the Long sweep low or above the Short sweep high.
- Fixed target at the latest confirmed opposing liquidity at signal time: swing high for Long, swing low for Short.
- Capture bracket before V1 clears completed setup state; do not trail it using later pivots.
- Reject missing levels or a bracket on the wrong side of signal close. This is an explicit execution-layer filter, not a new V1 detector rule.
- A gap at next open can make a previously valid bracket unfavorable; fills use TradingView's emulator and are not guaranteed at the signal close.
- One position, no pyramiding, no reversal; signals during an open position are ignored. Simultaneous Long/Short signals are skipped.
- Initial simulated capital USD 10,000. Order size 10% of equity as notional (NOT 10% risk per trade). 100% margin.
- Assumed commission 0.1% per fill, slippage 10 minimum ticks on applicable orders. These are research inputs, not verified broker fees.
- Both Long and Short simulated on spot OHLC; short borrow availability/cost, funding, spreads and latency are not modeled.
- Stop/target exits use ordinary OHLC broker-emulator assumptions; Bar Magnifier and tick/order-fill recalculation disabled.
- Any remaining position is closed on the boundary bar close, using immediately=true solely for window liquidation.
- No real orders, alert routing or external connection.

## Interpretation
The V1 indicator alone is not a strategy; performance depends on this protocol. No tuning, parameter search or target fallback is applied to manufacture trades. A zero-trade result is an execution-coverage finding, not a win rate or evidence of safety/profitability. Successful compilation does not independently prove non-repainting.

## Runtime diagnostics
The wrapper records first loaded bar, counted in-window signal-evaluation bars, raw Long/Short signals, eligible entries, invalid brackets, closed trades and net P/L. The last rejected signal's bar-open timestamp, reason and levels are displayed. Strategy Tester may show the broader loaded chart period; the code's date filter defines the actual entry window.

TradingView engine reference: https://www.tradingview.com/pine-script-docs/concepts/strategies/
