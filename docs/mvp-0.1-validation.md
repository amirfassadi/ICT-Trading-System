# MVP 0.1 — Detector validation checklist

## Scope
Indicator only. No orders, risk-model changes, parameter optimization or claims of profitability.

## Code review fixes
- Retracement into the active FVG now advances directional state only on confirmed candle close. Intrabar overlap is provisional.
- A new sweep only starts a setup if that direction is idle. This prevents silently overwriting an in-flight setup and leaving a stale active FVG box.
- LONG and SHORT are kept independent. Opposite-direction setups can remain active simultaneously.

## Manual TradingView checks (not yet executed)
1. Load `src/ict_detector_v1.pine` from this branch into Pine Editor, compile, and verify zero errors.
2. With Bar Replay, touch an active FVG intrabar, then move away before close. Verify no confirmed RET marker or state advancement.
3. Touch an active FVG and close with the bar range overlapping it. Verify exactly one RET transition.
4. During an active Long state 2 or 3, create a fresh SSL sweep. Verify the original FVG and its state remain active, and no replacement box is created.
5. Repeat the prior case for Short / BSL.
6. After a completed signal or timeout, verify the direction returns to idle and a new sweep can start a setup.
7. Create concurrent opposite-direction setups and verify isolation.
8. Validate original tests in `tests/test-cases.md`, including pivot confirmation timing and micro-MSS reference-bar timing.

## Acceptance criteria
- Pine Script v6 compiles successfully on TradingView.
- No confirmed state transitions appear before bar close.
- At most one active setup per direction, no orphaned active FVG boxes.
- The 23 existing manual scenarios are evaluated and failures recorded.
- No strategy/backtest transition until these checks have passed.

## Limitation
Static source inspection only. TradingView compilation, Bar Replay and real-time checks have not been performed; this change is **unverified**, not a passed test.
