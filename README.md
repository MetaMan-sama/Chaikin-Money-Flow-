# Volume Spread Analysis Alert — MQL4 Script

A MetaTrader 4 script that applies **Volume Spread Analysis (VSA)** methodology to detect climactic volume events, accumulation phases, and distribution phases by cross-referencing tick volume magnitude against bar spread width relative to their respective rolling averages over a configurable lookback window.

---

## Overview

Volume Spread Analysis is a price-action framework that interprets the relationship between tick volume and bar spread (high minus low) to infer institutional activity. This script operationalizes VSA by independently computing rolling average volume and average bar spread using `iVolume()` and `iHigh()`/`iLow()`, then classifying the current bar into one of four distinct VSA signal types — climactic wide-spread, climactic narrow-spread, accumulation, or distribution — each carrying a specific market-structure implication for the trained trader.

---

## Features

- **Four VSA signal types:** Climactic Volume (Wide Spread), Climactic Volume (Narrow Spread), Accumulation (Upward Narrow Spread), Distribution (Downward Narrow Spread)
- **Dual rolling averages** — independent `averageVolume` and `averageSpread` computed via `iVolume()` and `iHigh()`/`iLow()` over `LookbackPeriod` bars
- **Multiplier-threshold comparison** — current bar values compared against `VolumeThreshold × avgVolume` and `SpreadThreshold × avgSpread`
- **Close vs. open directional bias** — accumulation/distribution signals use `iClose()` vs `iOpen()` to determine bar direction under narrow spread and elevated volume conditions
- **Three notification channels:** sound alert, email, and mobile push
- **Lightweight loop** — polls once per minute (`Sleep(60000)`) to minimize CPU overhead
- All detected signals and their classifications are logged in full to the MT4 **Experts** tab for post-session review

---

## How It Works

1. Every minute, `AnalyzeVSA()` validates sufficient bar history then calls `CalculateAverageVolume()` and `CalculateAverageSpread()` — both iterate over `LookbackPeriod` bars and return simple arithmetic means via `iVolume()` and `(iHigh() − iLow())` respectively
2. Current bar volume (`iVolume(..., 0)`) and current spread (`iHigh(..., 0) − iLow(..., 0)`) are fetched
3. Four signal conditions are evaluated in priority order:
   - `currentVolume > VolumeThreshold × avgVolume` **and** `currentSpread > SpreadThreshold × avgSpread` → **Climactic Volume (Wide Spread)**
   - `currentVolume > VolumeThreshold × avgVolume` **and** spread below threshold → **Climactic Volume (Narrow Spread)**
   - `currentVolume > avgVolume` **and** `currentSpread < avgSpread` **and** bearish close → **Distribution (Downward Narrow Spread)**
   - `currentVolume > avgVolume` **and** `currentSpread < avgSpread` **and** bullish close → **Accumulation (Upward Narrow Spread)**
4. Non-empty signal string triggers `AlertVSA()` across all enabled notification channels

---

## Input Parameters

| Parameter           | Type            | Default     | Description                                                          |
|---------------------|-----------------|-------------|----------------------------------------------------------------------|
| `TradeSymbol`       | string          | `EURUSD`    | Symbol for analysis                                                  |
| `Timeframe`         | ENUM_TIMEFRAMES | `PERIOD_H1` | Timeframe for analysis                                               |
| `LookbackPeriod`    | int             | `14`        | Bars used to compute rolling average volume and spread               |
| `VolumeThreshold`   | double          | `1.5`       | Multiplier above average volume required for climactic classification|
| `SpreadThreshold`   | double          | `1.5`       | Multiplier above average spread required for wide-spread classification|
| `EnableAlerts`      | bool            | `true`      | Fire an on-screen/sound alert                                        |
| `EnableEmail`       | bool            | `false`     | Send an email notification                                           |
| `EnablePush`        | bool            | `false`     | Send a mobile push notification                                      |

---

## Alert Message Format

```
Climactic Volume Detected (Wide Spread) detected on EURUSD (Timeframe: PERIOD_H1)
```

---

## Installation

1. Copy `Chaikin_Money_Flow__CMF__001.mq4` to `MQL4/Scripts/` in your MT4 data folder
2. Compile in MetaEditor (F7)
3. Drag onto any chart from Navigator → Scripts
4. Configure inputs and click **OK**

> **Note:** MT4 tick volume is a proxy for true transactional volume. VSA signals are most reliable on instruments where tick volume correlates closely with real traded volume (e.g. forex majors during active sessions).

---

## Requirements

- MetaTrader 4 (`#property strict` compatible build)
- MQL4 compiler (MetaEditor)

---

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
