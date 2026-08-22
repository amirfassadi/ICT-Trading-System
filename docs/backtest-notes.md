# Backtest Notes

Historical backtesting is **Phase 3** and is intentionally not implemented in V1.

## Reserved design requirements

A future strategy/backtest implementation must:

- Preserve pivot confirmation delay; never assign a pivot signal to its reference candle.
- Preserve confirmed-close requirements for sweep, MSS, and micro MSS.
- Keep displacement separate from structural breaks.
- Record the sweep extreme for future risk calculations.
- Derive, but do not assume, future risk rules: long SL below SSL sweep extreme, short SL above BSL sweep extreme, long TP at opposing BSL, and short TP at opposing SSL.
- Define invalidation, setup expiration, fills, slippage, commission, and one-signal/one-position policy before coding.

The current indicator's LONG and SHORT markers are research events, not executable entries. Do not use them as evidence of profitability until an independently reviewed backtest exists.
