# RSI + ICT/FVG A/B research protocol

Status: experimental; Long/Short, standard BINANCE:BTCUSDT 1h. Fixed before evaluating performance on 2026-10-19. This is a separate strategy and does not replace ICT Detector V1.

## Accepted oscillator rule
LONG: RSI(14) crosses above SMA(14) of RSI on a confirmed close, and the minimum RSI over the current and previous four bars is strictly below 20. The RSI at crossover need not remain below 20.

SHORT (user-approved steering before the A/B run): RSI crossed below the same SMA and the maximum RSI in the same five-bar window was strictly above 80. All structure, entry and risk rules below are mirrored for Short.

A: oscillator + ICT/FVG; B: same with an additional Stochastic confirmation. Stochastic is the ordinary price stochastic (not Stoch RSI): raw lookback 14, K=SMA(raw,3), D=SMA(K,3). LONG: K crosses above D with BOTH lines strictly below 30. SHORT: K crosses below D with BOTH lines strictly above 70 (the symmetric inverse).

## Deterministic structure and ordering
- Confirm external high/low with pivots of length 2; the latest levels stored at the prior bar close are usable. Never backdate decisions to pivot origin bars.
- A bullish sweep is low < prior confirmed low and close > that low. Capture the sweep candle low and the latest confirmed high as fixed anchors.
- Sweep starts a setup only while flat and idle; ignore subsequent sweep restarts until reset.
- RSI confirmation, optional Stoch confirmation, and FVG must all be known during bars 0..5 inclusive after the sweep. Oscillator events before the sweep do not count.
- A later bullish candle closes above the high captured at sweep and has body >= 1.5*ATR(14) and body/range >= 0.70. Record the FIRST such displacement/MSS.
- The immediate next bar must form the standard bullish FVG: low > high[2], gap >= 0.10*current ATR. The displacement/MSS candle is its middle candle. No unrelated later FVG substitution.
- Readiness is the first bar when the FVG and all required confirmations are known. FVG may precede oscillator confirmation within the 5-bar window. Pre-ready retests are not entries.
- Wait up to 10 bars after readiness. Only the FIRST later candle whose range overlaps the FVG is considered. It must close bullish (close>open) with a valid stop distance to submit entry. A bearish/doji first touch invalidates setup.
- Any bar after the sweep trading at or below its low invalidates setup before entry. Confirmation deadline exceeded or retest deadline exceeded expires the setup.
- No Micro MSS requirement in this new proposal.

SHORT structure mirror: reclaim below a swept swing high; capture sweep high and prior swing low; bearish displacement closes below that low, immediately followed by a bearish FVG (high < low[2]); first post-ready overlap candle must close bearish. Touching/exceeding sweep high invalidates. Long/Short states are independent. If both produce an entry candidate on the same bar, skip both. Opening any trade clears both pending setups; no reversal.

## Execution assumptions
Next-available-tick market entry (normally next bar open); stop fixed one minimum tick below sweep low. Target fixed at signal close + 2*(signal close-stop). This is planned 2R referenced to the known signal close: next-open gaps/slippage change the actual achieved R. Neither stop nor target is moved using future bars. Initial simulated capital USD10,000, 10% equity NOTIONAL per trade (not 10% risk), no leverage/pyramiding/reversal. Short stop is one tick above sweep high; target = signal close - 2*(stop-signal close). Shorts on spot candles are hypothetical; short borrow/funding/availability is not modeled.

Assumed 0.1% commission EACH side and 10 minimum ticks slippage. Ordinary TradingView OHLC broker emulator, no Bar Magnifier, no intrabar/order-fill recalculation. No spread/funding/latency model; not verified exchange fees.

Primary entry window: 2026-07-21 00:00 UTC to 2026-10-19 00:00 UTC, final boundary candle excluded from new entries to avoid next-open entry beyond end. Earlier loaded candles warm up oscillator/pivots, but setups start only in-window. Close remaining positions at the boundary bar close. Keep parameters fixed for A/B; do not optimize after observing results.

## Verification scope
Inspect compilation, date coverage, phase counters, and built-in Strategy Tester output. A/B need not have identical sweep counts if positions prevent new setup generation. Results with few or zero trades cannot establish Stoch's value or profitability. All accepted conditions are research hypotheses, not established performance.

Engine documentation: https://www.tradingview.com/pine-script-docs/concepts/strategies/
