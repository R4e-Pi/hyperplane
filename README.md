# Hyperplane

Experiments in visualizing four spatial dimensions (X, Y, Z, W — with ±W as *Ana* and *Kata*), and in mapping Bitcoin consensus onto that geometry: the confirmed chain as a 3D hyperplane cross-section, the mempool as the unsliced space Ana of it, and the discarded as what recedes Kata.

Everything here is a single self-contained HTML file. Open it in a browser — no build step. Three.js r128 is pulled from cdnjs.

## Contents

### `sandbox/` — the Skew Sidestep 4D sandbox
A true cross-section renderer: every object has a W-extent and is drawn (and collides) only when the viewer's W is inside it. Demonstrates the "skew sidestep" — walking past an impassable 3D wall by translating along W, through a hyperplane where the wall does not exist, and returning on the far side.

| file | notes |
|---|---|
| `skew-sidestep-v2.html` | **Current.** Built against `docs/spec-rev2.md`. |
| `skew-sidestep-v1.html` | First implementation of the original spec; kept for the diff. |

**Controls:** `W/S` ±X · `A/D` ±Y · `Q/E` −W/+W · drag to orbit · scroll to zoom · `V` first/third person · **Auto-run sequence** plays the six-step maneuver deterministically.

What to look for:
- The wall goes solid → ghost as you leave the base slab (|W| ≤ 0.05). Ghosts are fog-exempt so they stay legible.
- The X–W minimap (bottom-right) is the proof: the wall is a rectangle at the origin, the bypass is a horizontal line above it — they neither intersect nor run parallel. Skew.
- Try the 4D collision the spec's sequence doesn't exercise: sidestep to W = 3, walk to X ≈ 0, press `Q`. The return is blocked — you'd materialize inside the wall.

### `mempool/` — Hyperplane mempool visualizer
The first prototype, and the other rendering approach: **projection**, not cross-section. A tesseract (real 4D rotation matrices in the XW/YW/ZW planes, perspective divide `d/(d−w)`) with a simulated Bitcoin node feed:
- Unconfirmed transactions arrive from deep Ana and settle at `w ∝ 1/feerate`.
- RBF re-presents the same 4D position at a higher fee; the original recedes Kata.
- Mining a block absorbs the top-fee transactions through the hyperplane and rolls the tesseract a quarter-turn in XW — the candidate-template cell becomes the confirmed-tip cell.
- The base W-rotation is decoded from the compact `bits` field (real mantissa/exponent codec); every retarget turns the whole object.

`Node` is a simulator with a ZMQ-shaped event surface (`onTx`, `onBlock`, `onRBF`, `onEvict`). Swap its timers for a WebSocket relay off a real node and the visualization is unchanged.

### `docs/`
- `spec-rev2.md` — the standardized sandbox spec (v2 is built to it).
- `implementation-report-v1.md` — the hand-off report describing the v1 build, its deviations from the original spec, and known weaknesses. This is what produced spec rev 2.

## Roadmap
- Wire `mempool/` to a live node (ZMQ `rawtx`/`rawblock` → WebSocket).
- Stack Mode: per-transaction chip stack (`Vx² + Vy² + Vz² + Vw² = 1`) with an orthographic toggle.
- False-intersection demo: a line skew to the hyperplane that appears to touch it until YW is nudged (zero-conf).
- PVE direction: the sandbox's wall + W-translation *is* the fee wall + RBF bump. Give W-translation a cost and the game starts.
