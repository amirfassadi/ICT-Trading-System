# ICT Detector V1 Strategy Specification

## Scope

V1 is an indicator-only implementation of:

`Liquidity Sweep → Displacement → Market Structure Shift (MSS) → Fair Value Gap (FVG) → Retracement → Micro MSS → Signal`

No order, broker, exchange, or live-trading behavior is included.

## 1. Swing detection

- Default swing length: `2`.
- Maximum setup bars default: `20`. This is a configurable robustness parameter, not an ICT rule and not an optimized value.
- A swing high is `ta.pivothigh(high, swingLength, swingLength)`.
- A swing low is `ta.pivotlow(low, swingLength, swingLength)`.
- The pivot price belongs to the reference candle at `bar_index - swingLength`.
- The pivot becomes usable only on the current confirmation bar. This is an unavoidable confirmation delay and is not treated as a signal on the reference candle.

## 2. Liquidity

- Buy-side liquidity (BSL) is represented by liquidity above the latest valid confirmed swing high.
- Sell-side liquidity (SSL) is represented by liquidity below the latest valid confirmed swing low.
- V1 deliberately uses only the latest confirmed swing high/low. It does not infer equal highs/lows, pools, or relative importance.

## 3. Strict liquidity sweeps

All conditions use confirmed bar data on the current bar.

### Bullish SSL sweep

`low < latest valid swing low` and `close > latest valid swing low`.

### Bearish BSL sweep

`high > latest valid swing high` and `close < latest valid swing high`.

V1 does not use a multi-bar or flexible reclaim.

## 4. Displacement

- ATR length default: `14`.
- Displacement ATR multiplier default: `1.5`.
- Minimum body/range ratio default: `0.70`.
- `body = abs(close - open)`.
- `range = high - low`.

Bullish displacement requires `close > open`, `body >= multiplier * ATR`, and `body / range >= ratio`. Bearish displacement uses the inverse candle direction with the same quality thresholds.

Displacement is a movement-quality condition. It is kept separate from MSS.

## 5. Market Structure Shift

- Bullish MSS: `close > last valid swing high`.
- Bearish MSS: `close < last valid swing low`.
- The break is by confirmed close, never by wick.
- No ATR buffer or subjective interpretation is used.
- The reference is the latest confirmed valid opposite-direction swing available before the displacement/MSS bar. The implementation stores the level and bar index at pivot confirmation and does not replace it with future pivots retroactively.

## 6. Fair Value Gap

The standard three-candle definitions are used:

- Bullish FVG: `low[0] > high[2]`; size `low[0] - high[2]`.
- Bearish FVG: `high[0] < low[2]`; size `low[2] - high[0]`.
- Minimum size default: `0.10 * ATR`.

For V1, a valid setup FVG must be formed on the displacement bar that also produces the associated MSS. Random unrelated FVGs are ignored.

## 7. Retracement

After a valid setup FVG is created, retracement is detected when the current confirmed bar range overlaps the FVG zone. No 50% level, Consequent Encroachment, OTE, Order Block, Premium/Discount, Kill Zone, SMT, or higher-timeframe bias is required.

## 8. Micro MSS

- Micro swings use the same configurable swing length, default `2`.
- Micro structure is tracked separately for Long and Short setups and separately from the external swing used for MSS.
- Each direction stores its own micro pivot price and actual pivot/reference bar index.
- A pivot becomes known on its confirmation bar, but its actual structural formation bar is `bar_index - swingLength`.
- Micro pivots are considered only after price has entered the active setup FVG, and the actual pivot/reference bar must be after that direction's retracement bar.
- Bullish micro MSS: confirmed close above the latest valid micro swing high.
- Bearish micro MSS: confirmed close below the latest valid micro swing low.
- Wicks do not confirm micro MSS.

Therefore, a pivot confirmed after retracement but formed before retracement cannot confirm Micro MSS. Long and Short use independent retracement bars and cannot read one another's micro structure.

## 9. Signals

LONG requires, in order: valid SSL sweep, bullish displacement, bullish MSS, associated bullish FVG, retracement into that FVG, and bullish micro MSS. SHORT is the exact inverse using BSL and bearish conditions.

Signals are indicator events only.

## 10. Future risk architecture

No orders are implemented. A future strategy may derive long SL below the SSL sweep extreme, short SL above the BSL sweep extreme, long TP at opposing BSL, and short TP at opposing SSL.

## 11. Anti-repainting policy

The script does not use `lookahead_on` or future bars to trigger events. Confirmed pivots necessarily use later bars for confirmation. Visual pivot markers can appear on the historical pivot candle using an offset, but state, sweep, MSS, FVG, retracement, and signal logic runs when data is available on the confirmation bar.

State 1 (sweep observed) expires when the displacement + MSS + FVG event has not occurred within `maxSetupBars`. State 2 (FVG active) expires when retracement has not occurred within `maxSetupBars` of FVG creation. Expiration resets only the corresponding directional state and its data. Its FVG box is stopped at the expiration bar so it does not imply an active setup indefinitely.
