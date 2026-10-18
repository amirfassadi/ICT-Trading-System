# Manual Test Cases

Use a liquid symbol, replay mode where possible, and the default settings (`Swing Length = 2`, `ATR Length = 14`, displacement multiplier `1.5`, body/range `0.70`, minimum FVG `0.10 ATR`). Record the bar where the marker appears and the bar where the information actually became available.

## 1. Valid bullish SSL sweep

After a confirmed swing low, create a candle with `low < swing low` and `close > swing low`. Expect a green SSL Sweep marker and no requirement for the wick alone to qualify.

## 2. Invalid bullish sweep

Create a candle that trades below the latest swing low but closes at or below it. Expect no bullish sweep. A candle that only touches the level also fails because the comparison is strict.

## 3. Valid bearish BSL sweep

After a confirmed swing high, create a candle with `high > swing high` and `close < swing high`. Expect a red BSL Sweep marker.

## 4. Invalid bearish sweep

Create a candle that trades above the latest swing high but closes at or above it. Expect no bearish sweep.

## 5. Bullish displacement

Create a bullish candle with body at least `1.5 * ATR` and body/range at least `0.70`. Expect a bullish displacement marker. A large wick with a small body must not qualify.

## 6. Bearish displacement

Create the inverse bearish candle and verify the bearish marker and the same thresholds.

## 7. Bullish MSS

After a confirmed swing high, close above that high. Expect bullish MSS; a wick above with a close below must not qualify.

## 8. Bearish MSS

After a confirmed swing low, close below that low. Expect bearish MSS; a wick below with a close above must not qualify.

## 9. Bullish FVG

On a three-candle sequence, make `low[0] > high[2]` and the gap at least `0.10 * ATR`. It should become a setup FVG only when it is also the valid bullish displacement/MSS bar after a bullish sweep.

## 10. Bearish FVG

On a three-candle sequence, make `high[0] < low[2]` and the gap at least `0.10 * ATR`. Verify the inverse association rule.

## 11. Full bullish setup

1. Confirm a swing low.
2. Produce strict SSL sweep.
3. On a later bar, produce bullish displacement, close above the latest valid swing high, and a qualifying bullish FVG on that same bar.
4. Return into the FVG zone.
5. After retracement, form and confirm a micro swing high, then close above it.

Expect exactly one LONG signal on the confirming close.

## 12. Full bearish setup

Follow the inverse sequence: confirmed swing high, strict BSL sweep, bearish displacement/MSS/FVG on one bar, return into the bearish FVG, then close below a confirmed micro swing low. Expect one SHORT signal.

## 13. False or invalid setups

Verify no final signal when any stage is missing: sweep after displacement, displacement without MSS, MSS without the associated FVG, FVG below minimum size, no retracement, or micro MSS before retracement.

## 14. Pivot confirmation delay

Create a visually obvious pivot. Confirm that its marker is drawn on the pivot reference candle, but the stored level, sweep/MSS logic, and dependent events cannot use it until `swingLength` bars later.

## 15. Repainting/lookahead edge cases

Use Bar Replay and inspect realtime progression. Confirm that a future pivot does not retroactively create a prior sweep, MSS, FVG, retracement, or signal. Confirm that wick-only breaks do not create MSS and that no signal appears before its close-confirmation bar.

## 16. Pre-retracement pivot confirmed later

Make a pivot whose confirmation bar occurs after retracement, but whose actual reference bar (`confirmation bar - swing length`) is before the retracement bar. Expect it to be ignored for Micro MSS.

## 17. Post-retracement pivot confirmed later

After a Long retracement, form a micro swing high whose actual reference bar is after the Long retracement bar. Once it is confirmed, a later confirmed close above it may produce bullish Micro MSS. Repeat the inverse for Short.

## 18. Long/Short state isolation

Create overlapping Long and Short partial setups. Verify that completing or expiring Long does not clear Short FVG, sweep, retracement, or micro structure, and vice versa.

## 19. Retracement-bar isolation

Arrange for Long and Short to retrace on different bars. Verify each Micro MSS uses only its own stored retracement bar and never the other direction's timestamp.

## 20. Setup expiration

With `Maximum Setup Bars = 3`, create a sweep and then wait beyond three bars without a valid displacement + MSS + FVG. Expect the corresponding setup to reset. Separately create a valid FVG and wait beyond three bars without entering it; expect State 2 to reset.

## 21. FVG expiration visualization

After either expiration, verify that its FVG box ends at the expiration bar and does not continue extending as an active zone. The opposite direction's box must remain unaffected.

## 22. Valid Micro MSS

After a valid retracement, form a confirmed micro pivot with its actual reference bar after retracement, then close beyond that pivot in the setup direction. Expect the direction-matched Micro MSS marker and final signal.

## 23. Invalid Micro MSS from pre-retracement pivot

Use a micro pivot that is confirmed after retracement but whose actual reference bar is before retracement. Close beyond its level. Expect neither a direction-matched Micro MSS nor a final signal.

## 24. Retracement requires a confirmed candle

On a realtime, not-yet-confirmed bar, let its developing high/low overlap the active FVG. Verify that no retracement event is committed before the candle closes. At the confirmed close, the retracement may register if the completed candle range overlaps the zone. Repeat for Long and Short. Compare Bar Replay behavior with historical bars.
