# Nokia 3310 Snake II — inside Microsoft Excel

> A pixel-accurate Snake II clone running entirely in a spreadsheet, with no plugins, no canvas, no WebGL — just cell background colors.

Excel's cell-formatting engine, repurposed as a pixel renderer. Each cell is resized to 4 × 4 px and painted via `setCellProperties`, turning the worksheet into a monochrome Nokia display. Wrap-around movement, timed bonus items, original score multipliers — mechanically faithful to the Nokia original.

[▶ Compare to the original](https://www.youtube.com/watch?v=0gPbNhFjm1o)

---

## Demo

https://github.com/user-attachments/assets/1cc8b7c0-2a7f-4ed2-9a80-2b9ef6979b57

---

## How it works

The entire game runs inside a Script Lab snippet. Excel's worksheet becomes a display: cells are shrunk to 4×4 and painted black or Nokia-green via batch `setCellProperties` calls.

**Sprites** — every tile (snake head, body, tail, digits, food, bonus items) is encoded as an integer bitfield and decoded once at startup into a `SettableCellProperties[][]` matrix. Drawing a tile at runtime is a pure table lookup.

**Game state runs in $O(1)$ per tick** using three custom data structures:

| Structure | Role |
|-----------|------|
| Bit-packed circular buffer | 2-bit-per-direction segment ring in a `Uint32Array` |
| Sparse set (×2) | $O(1)$ random free-cell selection for fruit and special placement; $O(1)$ collision |
| BitSet | Per-cell digestion flag for accurate body rendering |


Everything is allocated once at game start and updated in-place — no GC pressure, no array scans, no resizing. All of which adds up to <ins>butter-smooth gameplay inside a spreadsheet</ins>.


---

## Gameplay

- Eat fruit to grow and score. Score = **fruit × level**.
- Every 5 fruits spawns a **bonus item** with a countdown timer. Eating it scores `5 × level + 2 × time_remaining + 7` like the original — the faster you reach it, the more it's worth.
- The board wraps. Running into yourself ends the game.
- Fill the board completely to win.

---

## Controls

| Key | Role |
|-----------|------|
| `W` / `↑` | Move up |
| `A` / `←` | Move left |
| `S` / `↓` | Move down |
| `D` / `→` | Move right |



---

## Getting started

Requires the [Script Lab](https://learn.microsoft.com/en-us/office/dev/add-ins/overview/explore-with-script-lab) add-in for Microsoft Excel (free, from Microsoft).

**Option A — import from Gist** *(faster)*

Open Script Lab → *Import* and paste:

```
https://gist.github.com/3ez5me/29cbdfea51fa0a5dd546dfe916492b78
```

**Option B — copy manually**

Copy `snake.yaml` into a new Script Lab snippet.

### Walkthrough

https://github.com/user-attachments/assets/4a9732ef-124d-46b4-b44d-cc82ae9c4955

---

## Settings

| Option | Default | Range | Effect |
|--------|---------|-------|--------|
| Level | 5 | 1–9 | Speed and score multiplier |
| Width | 20 | ≥ 8 | Grid columns |
| Height | 11 | ≥ 2 | Grid rows |
