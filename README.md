# Fractal Model

**A TradingView Pine Script v6 indicator for Candle Range Theory (CRT), higher-timeframe sweeps, and lower-timeframe entry models.**

Fractal Model brings higher-timeframe (HTF) context onto a lower-timeframe (LTF) chart. It identifies potential and confirmed CRT C2 sweeps, displays relevant levels and points of interest (POIs), and looks for two entry patterns inside the active C2 candle: **Model #1 (TBS → M1)** and **Model #2 (FVG tap → rejection)**.

The current build is an **indicator**, not an automated trading strategy or order-execution bot. Its signals are intended for chart analysis and manual review.

## Features

- **CRT sweep and C2 detection:** tracks when C2 wicks beyond C1 high/low and price body-closes back within the relevant level.
- **1-minute / 1-hour synchronisation:** references the previously completed 1H candle's actual high and low when running a 1H model on a lower-timeframe chart.
- **C1 range display:** CRT High, CRT Low and equilibrium (EQ), plus optional pending `?C2` and confirmed `C2` labels.
- **Model #1:** local TBS validation (optional 2–6-candle contained structure), subsequent M1 body-close entry, Entry and SL drawings.
- **Model #2:** direction-aligned LTF FVG after the TBS gate, later FVG tap and rejection body-close, Entry and SL drawings.
- **Cleaner entries:** by default, shows **only one confirmed entry per C2** across both entry models; a new manipulation extreme resets older candidates.
- **CISD and IC-CISD:** reversal/continuation levels, stage visibility and optional criteria-gated IC-CISD alerts.
- **HTF POIs:** FVG, inverted FVG (IFVG), order block (OB), rejection block (RB), old high/low pivots (OHP/OLP) and anchored open/close levels (OCL), with selectable C2 checklist options.
- **Market context:** projected HTF candles, optional additional 1H/4H/Daily candle panels, SMT comparisons, PSP divergence, C3/C4 entry zones and compact CRT dashboard.
- **Diagnostics:** optional C1 high/low plots, breach and tracked-sweep markers, and confirmation values in TradingView's Data Window.

## Quick start

1. Open **TradingView → Pine Editor**.
2. Open the latest `fractal_clean_single_entry.pine` file and copy its contents into a new Pine script.
3. Save the script, then select **Add to chart**. The script uses **Pine Script v6** and imports `fstarcapital/AssetCorrelationUtils/1`; TradingView must be able to access that library.
4. For the main 1-minute model, choose a **1m chart**, set **CRT alignment** to **`1m-1H`**, and check that the chart's symbol and data feed are the ones you want to analyse.
5. Under **CRT C2 timing & POIs**, enable the relevant NY CRT hour (for example **09:00** for a 09:00–10:00 C2), **Show pending C2 sweeps**, and **Show C1 CRT high/low + EQ**.
6. Under **CRT LTF Entry Model**, enable the entry models you want, keep **Only one confirmed entry per C2** on for a cleaner chart, and enable **Show entry-model drawings**.
7. Use **Bar Replay** to review a historical session candle by candle. Check that the C1 levels, sweep, confirmation, TBS and entry appear at the expected stages.

> **Time zones:** the selected CRT key hours and model tooltips use `America/New_York`. TradingView's chart clock may be configured to a different zone, so match times carefully.

## How the CRT model works

### 1. C1 reference

**C1** is the previous completed higher-timeframe candle. Its high and low form the CRT reference levels. On a 1-minute chart running the 1H model, the indicator requests the **completed previous 1H** high/low to keep its sweep reference aligned with the hourly chart.

### 2. C2 liquidity sweep and confirmation

The active **C2** candle can create:

- A **bullish setup** after a wick trades below **C1 Low** and price body-closes back **above C1 Low**.
- A **bearish setup** after a wick trades above **C1 High** and price body-closes back **below C1 High**.

A wick-only breach can be drawn as **`?C2`** when pending sweeps are enabled. The entry models require a **confirmed** sweep; a wick alone is not an entry signal. The developing setup must also remain within the permitted active C2 hour and pass applicable timing/visibility rules.

The final C2 state is checked when the HTF candle completes. A candidate that fails to confirm is not treated as a confirmed historical setup.

### 3. Model #1 — TBS / M1

The current code uses this sequence:

1. A qualifying C2 sweep is confirmed.
2. A **TBS** candle makes a direction-matching **body close back through the swept CRT H/L**.
3. If **M1 require 2–6 inside validation** is on, the TBS must break a nearby contained 2–6-candle body structure. The structure may have formed before the actual CRT sweep, but the TBS must occur after it.
4. The **M1 trigger** is the **TBS candle high** for bullish setups or **TBS candle low** for bearish setups.
5. A **later candle** must body-close past that M1 trigger in the trade direction to confirm the entry.
6. The script draws the TBS/M1 reference, **Entry** and **SL**. The stop is based on the manipulation extreme tracked since the sweep.

Turning the inside-validation setting **off** allows the script to use a simpler break of the previous candle's body. It **does not** bypass the required CRT sweep, confirmation, body return or active C2 window.

### 4. Model #2 — FVG tap rejection

Model #2 shares the C2 and TBS prerequisites. After the qualifying TBS, the indicator looks for a three-candle LTF FVG **in the setup direction**. It then waits for **a later candle to tap that FVG** and body-close out of the gap in the trade direction. The FVG's creation candle does **not** count as its own tap. Once confirmed, the script displays the Model #2 entry and stop.

### 5. One confirmed entry per C2

**Only one confirmed entry per C2** is **on by default**. Model #1 and Model #2 share this limit: after one entry confirms, additional confirmations for the same current C2 are suppressed. If C2 later creates a **new manipulation extreme**, stale entry candidates are reset and the model can search again. Turning the setting **off** restores multiple-entry behaviour.

This option reduces chart clutter; it does **not** guarantee that the first signal will remain valid if C2 continues manipulating price.

## Supported timeframe alignments

| CRT alignment option | Chart timeframe | HTF source |
| --- | --- | --- |
| `1m-1H` | 1 minute | 1 hour |
| `5m-4H` | 5 minutes | 4 hours |
| `1H-1D` | 1 hour | Daily |
| `4H-1W` | 4 hours | Weekly |
| `1D-1M` | Daily | Monthly |
| `Custom` | User-selected | User-selected |
| `Automatic` | Depends on chart | 1m → 1H, 5m → 4H; 1H/4H/Daily use their native sources |

The original source also contains older quarterly/session helper functions, but these are **not** offered as active CRT alignment choices in the current settings.

## Important settings

| Settings section | Controls |
| --- | --- |
| **General Settings** | CRT alignment, custom timeframes, directional bias, setup history and alerts |
| **CRT C2 timing & POIs** | NY key-hour filter, C2 POI checklist, C1 range, pending sweeps, dashboard, sweep debug |
| **CRT LTF Entry Model** | Model #1/2 enable, optional inside validation, one-entry-per-C2, max bars after sweep, drawing extension and colours |
| **Model Settings** | Sweeps, live setup display, C2/C3/C4 labels, CISD/IC-CISD and entry zones |
| **HTF Candles** | HTF candle projection, separators and Previous EQ |
| **Fair Value Gap** | HTF gap projection, mitigation mode, number of visible gaps, hide mitigated |
| **HTF Rejection & Order Blocks** | Bull/bear RB and OB visibility, confirmation criteria, boundary, invalidation and mitigation styling |
| **PSP Divergence** | Auto/manual correlation assets, polarity change versus pivot-dependent PSP, precise mode |
| **MTF candles & SMT** | SMT comparison symbol, 1H/4H/Daily options, additional candle panels |
| **Performance** | `Calculate on Close?` controls updates within a developing HTF candle |

**POI checklist note:** Some items are enabled by default (FVG, OB, OHP and OLP); IFVG, RB and OCL start disabled. Selecting **Show only checklist C2** restricts displayed C2 setups; with **Also show non-POI C2 at key times**, selected key-hour C2s can remain visible without a matching POI.

## Chart labels and abbreviations

| Label | Meaning |
| --- | --- |
| `C1` | Previous completed higher-timeframe reference candle |
| `?C2` | Pending wick sweep awaiting body-close confirmation |
| `C2` | CRT sweep/setup candle |
| `C3`, `C4` | Subsequent continuation/confirmation candles tracked by the indicator |
| `CRT H` / `CRT L` | C1 high / low reference levels |
| `CRT EQ` / `Previous EQ` | Midpoint reference for the relevant CRT candle/range |
| `TBS` | Local directional body-close break used to qualify the entry sequence |
| `M1`, `Entry`, `SL` | Model #1 trigger, confirmed entry reference, stop-loss reference |
| `M2 FVG`, `M2 Entry` | Model #2 gap and confirmed rejection entry |
| `CISD`, `IC-CISD` | Change in State of Delivery and its continuation variant |
| `FVG`, `IFVG` | Fair Value Gap and Inverted Fair Value Gap |
| `OB`, `RB` | Order Block and Rejection Block |
| `OHP`, `OLP`, `OCL` | Old High Pivot, Old Low Pivot, Open/Close Level |
| `PSP`, `SMT` | Correlated-asset divergence modules |

## Alerts

The script includes its own **Alerts?** toggle and emits Pine `alert()` calls for supported events, including C2 and IC-CISD conditions. It also includes a separately configurable criteria-gated IC-CISD alert. In TradingView, add an alert on the indicator and select the available **Any alert() function call** condition when appropriate. After changing indicator settings, review and, if necessary, recreate the corresponding TradingView alert so it uses your current configuration.

**Do not assume every chart label generates a separate alert.** In particular, the TBS/M1/M2 drawing paths should be checked in code before relying on automated entry notifications.

## Troubleshooting

### C2 is visible on 1H but missing on 1m

1. Verify the chart is **1 minute** and **CRT alignment** is **`1m-1H`** (not a mismatched custom mode).
2. Make sure both charts use the **same symbol and feed**.
3. Temporarily set **Only draw C2 at CRT hours** and **Show only checklist C2** to **Off**, **Bias** to **Neutral**, and **Hide Setups** to **Off**.
4. Enable **Show Sweeps**, **Show live setup** and **Show pending C2 sweeps**.
5. Turn on **DEBUG: plot checked C1 H/L and sweep status** to inspect the hourly reference used on the LTF.
6. In the debug view, a **green upward triangle** indicates the 1H C1 low was breached; a **red downward triangle** indicates its high was breached. An **orange circle** indicates the sweep was recognised by the setup engine. The Data Window's confirmation values are **-1 = no active sweep, 0 = wick-only, 1 = confirmed**.

If the raw breach appears without a tracked-sweep marker, investigate the C2 setup engine, not the entry model. Debug plots are for diagnosis and can be switched off during normal use.

### Sweep and C2 appear, but no M1 entry

Check that **Enable Model #1** is on, the sweep has a **body-close return through CRT H/L**, the TBS occurred **after** the sweep, and **a later candle body-closed through the M1 trigger**. Both confirmation steps must occur **inside the same active C2 hour**. To isolate inside-structure restrictions, temporarily disable **M1 require 2–6 inside validation**; other conditions still apply.

### No M2 entry

Check that **Enable Model #2** is on, the TBS prerequisite formed, a direction-matching FVG appeared **after** it, and a later candle **tapped and rejected** that FVG while C2 was active. If Model #1 already confirmed first and single-entry mode is on, Model #2 will not produce a second confirmed entry for that C2.

### Too many Entry/SL drawings

Turn on **Only one confirmed entry per C2** and **Show entry-model drawings**. If the chart still looks busy, lower setup history or disable non-essential POI, SMT, FVG and projection layers. Historical setups can still contribute drawings; this setting is **per C2**, not a chart-wide one-signal limit.

### No HTF projection, SMT or PSP data

Check the appropriate display settings and the symbol mapping. PSP's automatic mapping relies on the imported correlation utility, while manual mode requires correlated assets to be specified. SMT needs usable, time-aligned peer-market data; different symbols or trading hours can affect what appears.

## Technical notes and limitations

- **Language:** Pine Script **v6**, with `overlay = true`.
- **External dependency:** `fstarcapital/AssetCorrelationUtils/1` for correlated-asset logic.
- **Rendering limits:** the script declares up to **500 lines, 500 boxes and 500 labels**; data and drawing-history limits still apply.
- **Calculated states:** developing HTF candles can change before they close. A **pending** `?C2` is not equivalent to a final confirmed C2, and active entry candidates can be reset after a new extreme.
- **Timing:** entry validation depends on candle closes, LTF/HTF boundaries, available bar history and the trading session/data feed.
- **Not a strategy:** Entry and SL marks are analytical levels, not simulated fills, performance results or broker orders.
- **Verification:** this README is based on the current source file. The build has **not been independently compiled or fully regression-tested in TradingView** as part of writing this documentation.

## Suggested project layout

```text
fractal-model/
├── README.md
└── fractal_clean_single_entry.pine
```

## License and acknowledgement

The Pine source declares the **Mozilla Public License 2.0 (MPL-2.0)**. Preserve the existing source license notice and comply with the licence when distributing modified source.
## Disclaimer

Fractal Model is a technical-analysis indicator and educational charting tool. It does not guarantee profitable signals or replace independent risk management. Confirm behaviour on your instrument, data feed and timeframe before using any signal in live trading.
