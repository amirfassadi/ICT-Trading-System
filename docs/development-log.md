# Development Log

## Initial architecture

Phase 1 is a single Pine Script v6 indicator in `src/ict_detector_v1.pine`. The code is organized into:

1. Configurable inputs.
2. Small reusable candle, displacement, FVG, and zone-overlap functions.
3. Confirmed external swing and latest-liquidity tracking.
4. Directional setup state (`idle`, `sweep observed`, `FVG active`, `retracement observed`).
5. Separate Long/Short retracement bars and micro-swing tracking after retracement.
6. Optional visual outputs and alert conditions.

The indicator intentionally does not create orders or use a strategy declaration.

## Decisions

- `ta.pivothigh` and `ta.pivotlow` use the same configurable length for external and micro structure.
- A pivot is stored only when confirmed. Its plot is offset to the reference candle for readability, while dependent logic runs on the later confirmation bar.
- V1 uses only the latest confirmed high/low as the deterministic liquidity and MSS reference.
- A sweep is strict and same-candle: the wick crosses the reference and the close reclaims it.
- Displacement and MSS remain separate boolean conditions. A setup requires both on the same confirmed bar, and the same bar must form the associated FVG.
- Retracement is any confirmed bar whose range overlaps the active FVG zone after formation.
- Micro pivots are tracked only after retracement, keeping them separate from the external MSS level.
- Micro pivot confirmation time is separated from its actual reference/formation bar (`bar_index - swingLength`). A Micro MSS requires the reference bar to be after that direction's retracement bar.
- Long and Short each own their retracement bar and micro high/low values and bar indexes. Neither direction reads or resets the other's micro state.
- A completed signal resets that direction's state. No order or risk action follows it.
- `maxSetupBars` defaults to `20` and is a configurable robustness parameter, not an optimized ICT parameter. State 1 expires before the event sequence is completed; State 2 expires before retracement. Expiration clears only the corresponding state and data.
- Expired setup FVG boxes are stopped at the expiration bar, preventing an expired zone from appearing active indefinitely.

## Assumptions and limitations

- The user specification does not define a multi-bar association window, so V1 uses the exact same displacement/MSS/FVG bar association.
- Expiration is a robustness safeguard requested for this revision; it does not add a new ICT condition and does not optimize the default value.
- The latest swing simplification does not infer equal highs/lows, pools, inducement, or swing importance.
- Pine compilation and visual behavior require TradingView validation; no local Pine compiler is bundled.
- Historical pivot shapes are visually offset and can look earlier than their actual availability. The documentation and labels distinguish this presentation detail from signal timing.

## Known limitations

- This is not a backtestable strategy yet.
- There is no position model, stop-loss/target execution, commission/slippage model, or performance report.
- Alerts are informational indicator alerts only.
- The detector has no higher-timeframe, session, order-block, OTE, SMT, or premium/discount logic.

## Future work

Only after manual TradingView validation should the project consider Phase 2. Any strategy conversion must preserve event confirmation timing and explicitly define order, invalidation, and risk rules before implementation.

## 2026-10-19 — TradingView compilation and smoke validation

The unchanged V1 source compiled, was added to chart, and was saved privately in TradingView. Initial smoke checks passed on AAPL 1D, BTCUSDT 1D/15m and EURUSD 1h; input/display changes recalculated successfully and defaults were restored. No compiler fix was necessary. Intraday Replay was blocked by the Basic account's plan entitlement. The 24 manual behavioral cases remain pending systematic execution. See [validation report](tradingview-validation-2026-10-19.md) for evidence and exact limits.
