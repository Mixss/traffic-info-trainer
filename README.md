# Traffic Info Trainer

A browser-based radar trainer for practising air traffic information calls.  
No installation, no back-end — just open `index.html` (or the GitHub Pages URL) and start training.

---

## Purpose

Air traffic controllers and student controllers need to quickly read a radar picture and compose accurate traffic information calls in a standard format:

> *"Traffic, 12 o'clock, 8 miles, Boeing 737, Medium, Flight Level 350, crossing left to right."*

This tool generates randomised two-aircraft scenarios on a synthetic radar scope so you can practise building that mental picture repeatedly without needing a full simulator.

---

## What you see on screen

```
┌─────────────────────────────────┐
│                                 │
│   ○ A320/M          ◇ B77W/H   │
│     FL350             FL360     │
│        \   8.3 NM   /          │
│         ·-----------·           │
│                                 │
│         [ ▶ NEXT SITUATION ]    │
└─────────────────────────────────┘
```

| Symbol | Meaning |
|--------|---------|
| **Green circle** `○` | **Reference aircraft** — the one you are controlling / the pilot you are talking to |
| **Yellow diamond** `◇` | **Traffic aircraft** — the conflicting traffic you are reporting |
| **Heading line** | Short line extending from each symbol in the direction of travel |
| **Grey dashed line** | Straight-line distance between the two aircraft |
| **Label** | `ICAO type / WTC category` on the first line, altitude (`A0xx` below FL100, `FLxxx` above) on the second |

### Altitude format
| Display | Meaning |
|---------|---------|
| `A030` – `A090` | Below FL100 — altitude in hundreds of feet (A = Altitude) |
| `FL100` – `FL390` | Flight Level |

### Wake Turbulence Categories (WTC)
| Code | Category |
|------|----------|
| `L` | Light |
| `M` | Medium |
| `H` | Heavy |
| `J` | Super (e.g. A380) |

---

## How a scenario is generated

1. **Two random headings** are chosen from the full 360°, with a minimum angular separation of 10° so the aircraft are never flying on identical tracks.
2. **Crossing or missing:**
   - **50 %** of scenarios — the two flight paths intersect (classic crossing traffic).
   - **50 %** of scenarios — the traffic passes at a random lateral offset of **0.5 – 4 NM** (parallel, overtaking, or near-miss geometry).
3. **Altitudes** always differ by exactly **1 000 ft**, randomly assigned between `A030` and `FL390`.
4. **Aircraft types** are drawn at random from a pool of 20 common ICAO designators with correct WTC categories.
5. **Timing** — the point of closest approach occurs roughly **30 seconds** after each new scenario loads, giving you realistic time to compose and deliver the call.

---

## Radar scale

| Ring | Distance |
|------|----------|
| Inner (25 %) | 2.5 NM |
| 2nd ring (50 %) | 5 NM |
| 3rd ring (75 %) | 7.5 NM |
| Outer edge (100 %) | **10 NM** |

---

## Controls

| Control | Action |
|---------|--------|
| **▶ Next Situation** button | Immediately generate a new random scenario |
| Window / screen resize | Automatically redraws and resets to a new scenario |

---

## Running locally

No build step required.

```bash
git clone https://github.com/Mixss/traffic-info-trainer.git
cd traffic-info-trainer
# open index.html in any modern browser
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

Or use any static file server:

```bash
npx serve .
# → http://localhost:3000
```

---

## GitHub Pages

The trainer is deployed at:  
**https://mixss.github.io/traffic-info-trainer**

To enable Pages on your own fork: **Settings → Pages → Source: `main` / `/ (root)` → Save**.

---

## Aircraft database

The following types are included in the random pool:

| Type | Name | WTC |
|------|------|-----|
| A320 | Airbus A320 | M |
| A321 | Airbus A321 | M |
| A333 | Airbus A330-300 | H |
| A359 | Airbus A350-900 | H |
| A388 | Airbus A380-800 | J |
| A124 | Antonov An-124 | H |
| AT75 | ATR 72-500 | M |
| B734 | Boeing 737-400 | M |
| B738 | Boeing 737-800 | M |
| B739 | Boeing 737-900 | M |
| B752 | Boeing 757-200 | M |
| B763 | Boeing 767-300 | H |
| B77W | Boeing 777-300ER | H |
| B788 | Boeing 787-8 | H |
| C172 | Cessna 172 | L |
| CRJ9 | Bombardier CRJ-900 | M |
| DH8D | Dash 8 Q400 | M |
| E190 | Embraer E190 | M |
| E75L | Embraer E175-E2 | M |
| F100 | Fokker 100 | M |

---

## Technical notes

- Pure HTML + Canvas 2D — zero dependencies, zero frameworks.
- **HiDPI / Retina support** — the canvas buffer is scaled by `devicePixelRatio` (capped at 3×) so text and lines are sharp on all screens including mobile.
- Sweep animation runs at the browser's native frame rate via `requestAnimationFrame`.
- Trail dots are recorded every 500 ms and fade with age (up to 5 historical positions per track).
