# Bullish‑and‑Bearish‑OB‑Finder · Pine Script v6 · 15‑min

Two companion indicators that **detect, draw and continuously update bullish and bearish
_order‑block_ zones** (OB) designed to work on TradingView NQ1! chart.  
Both scripts are pure Pine v6, run on a *higher* timeframe (configurable, default **15 min**),
and require **no repainting / look‑ahead** tricks – every boundary is formed strictly on
closed candles.

---

## 1 · What’s an Order‑Block?

> A price area built around a local extremum (swing high or swing low) whose
> supply/demand imbalance later **arrests** the trend and often sparks a reversal.

* **Bullish OB** – born from a **pivot‑low** (local minimum) followed by a bullish
  impulse.  
* **Bearish OB** – born from a **pivot‑high** (local maximum) followed by a bearish
  impulse.

The block expands while consecutive candles prove (or disprove) the imbalance and is
*finalised* once price breaks the extremum in the opposite direction.

---

## 2 · How the Algorithm Works

Below the two scripts are identical except for **polarity** (highs ↔ lows, green ↔ red).

| Phase | Action |
|-------|--------|
| **Primary BACK** | On the *first* confirmed pivot (low ↔ high) mark the next candle as `backBar`, start a 1‑bar box. |
| **Primary FORWARD** | Scan **right** until a candle’s body *engulfs* the first BACK‑wick ↔ start dynamic expansion.<br>Abort if price violates the pivot itself. |
| **Cycle** | Thereafter the engine alternates<br>**BACK** (scan left for deeper wicks / opposite pivots)<br>**FORWARD** (scan right for new bodies crossing the last BACK‑wick)<br>until the pivot is broken or look‑back limit reached. |
| **Aging** | Any zone older than `MAX_LOOKBACK` bars is deleted to keep charts clean. |

A pair of arrays – `lastBack*` and `lastFwd*` – remembers where each phase stopped, so
next bar the scan resumes **only from that point**.  
Result: *O(N)* total work no matter how many bars are on screen.

---

## 3 · User Parameters

| Name | Default | Meaning |
|------|---------|---------|
| `pivotLen` | **1** | How many bars left & right must be lower/higher to confirm an extremum. |
| `tf` | **15** | HTF used for pivot calculation and plotting. |
| `MAX_LOOKBACK` | **3000 / 4000** | Hard age cap per OB (can be tuned for performance). |

All other constants are explained inline in the code.

---

## 4 · Files

| File | Purpose |
|------|---------|
| `bear_ob_finder.pine` | Draws **red** rectangles for every evolving *bearish* OB. |
| `bullish_ob_finder.pine` | Draws **green** rectangles for every evolving *bullish* OB. |

Copy/paste either script into TradingView Pine Editor and press **Add to chart**.  
They can run together – arrays and colours are namespaced.
