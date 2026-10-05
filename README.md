[README (2).md](https://github.com/user-attachments/files/33056775/README.2.md)
# IFVG — Inverse Fair Value Gaps

A Pine Script v6 indicator for TradingView that tracks Fair Value Gaps and the moment they invert.

An FVG is a three-candle imbalance. When price comes back and closes through it, the gap doesn't just disappear — it flips polarity and becomes a zone that tends to hold from the other side. A bullish gap that gets violated turns into resistance; a bearish gap that gets violated turns into support. That flipped zone is the IFVG, and it's what this indicator is built around.

Most FVG scripts draw boxes and leave it there. This one tracks the full lifecycle: gap forms, gap gets violated, gap inverts, inverted zone eventually fails. Each stage is drawn differently so you can see where price is in that sequence.

---

## What it draws

**FVG zones** — the original three-candle imbalances, bullish and bearish, before anything happens to them.

**IFVG zones** — what the gap becomes after price closes through it. Same price coordinates, flipped polarity, new colour. `+IFVG` for support, `-IFVG` for resistance.

**C.E. line** — consequent encroachment, the 50% midpoint of every zone. Dotted, drawn automatically on both FVGs and IFVGs. Price respects the midpoint of a zone as often as the edges, so it's not optional here.

**Labels** — `+FVG` / `-FVG` / `+IFVG` / `-IFVG`, sitting just outside the right edge of each zone so the CE line never runs through the text.

---

## Volume imbalance encompassing

Wick-to-wick measurement undersizes a lot of gaps. If there's a body gap sitting next to the middle candle — a volume imbalance — that space is part of the same inefficiency, and the zone should include it.

With this on, zone boundaries expand from the wick levels out to the neighbouring candle bodies. Each side expands on its own, so one VI extends one edge, and when there are VIs on both sides of the middle candle both get pulled into a single zone.

On by default. Turn it off if you want strict wick-to-wick.

---

## Settings

### FVG Settings

| Setting | Default | What it does |
|---|---|---|
| Min FVG Size % | 0 | Ignores gaps smaller than this percentage of price. At 0 everything is detected. On NQ around 23,000: `0.02` ≈ 4.6 points, `0.05` ≈ 11.5 points. Useful for cutting micro-gaps on M1/M2. |
| Mitigation By | Close | `Close` requires a closing print through the zone. `Wick` triggers on any touch past the boundary. Close is stricter and produces fewer, cleaner flips. |
| Show Original FVGs | on | Draw gaps before they invert. |
| Show IFVG Zones | on | Draw the inverted zones. |
| Extend Boxes (bars) | 10 | How far right the zones project. |
| Max Zone Age (bars) | 50 | Zones older than this are dropped. Counted in bars, not clock time. |
| Encompass Volume Imbalances | on | See above. |
| Max FVGs Shown / Max IFVGs | 2 / 2 | **Display limit only.** Older FVGs stay tracked in the background and will still invert when price closes through them — they just aren't drawn. |

### Visual Settings

Fill colours for bull/bear FVG and bull/bear IFVG zones.

### Label Settings

Toggle, text colour, background colour, size, and horizontal alignment (Left / Center / Right). Right and Center track the zone's live edge as it extends. Vertical position is fixed to the zone midpoint.

CE appearance and the label text strings are hardcoded.

---

## Things worth knowing

**Everything processes on confirmed bars.** Mitigation is only evaluated once a candle closes. Running it on every tick lets an intrabar wick flip a gap and then invalidate the resulting IFVG inside the same candle, even when the candle closes back inside — which shows up as zones that should exist but don't. Confirmed-bar processing means what you see is what actually happened.

**Display count and detection are separate.** Capping how many zones are drawn doesn't stop older gaps from being tracked. This matters: if the two were tied together, any gap older than the display limit could never produce an IFVG.

**Age is counted in bars.** Not milliseconds. Zones survive session breaks, weekends and holiday gaps without expiring early, and the setting means the same thing on any timeframe.

**No session filter.** FVGs are detected on every bar. Session windows belong in a separate tool.

**Opening gaps aren't detected.** Only the three-candle pattern. Two-candle gaps where the wicks don't touch at all — NDOG, NWOG — are a different concept and need their own indicator.

**Consecutive gaps aren't merged.** Stacked FVGs stay as separate zones. Merging them would produce a CE line for a range that was never delivered, and the merged zone would only invert on a close through the whole combined range, which hides earlier inversions. If stacking bothers you, filter with Min FVG Size % instead.

---

## Alerts

- **Bullish IFVG Created** — a bearish gap inverted into support
- **Bearish IFVG Created** — a bullish gap inverted into resistance

Set up through TradingView's alert dialog by picking the named condition.

---

## Install

Pine Editor → paste `IFVG.pine` → Save → Add to chart.

Built on Pine Script v6. Works on any symbol and timeframe; built and tested on NQ/MNQ intraday.
