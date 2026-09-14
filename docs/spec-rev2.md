# Skew Sidestep — technical specification, revision 2

Standardized spec produced after the v1 implementation report. `sandbox/skew-sidestep-v2.html` is built against this document; every constant in its `SPEC` object cites a section below.

## 1. Spatial framework
- Euclidean 4-space (X forward, Y lateral, Z up, W = +Ana / −Kata).
- Physics, collision and HUD in spec coordinates; render mapping `render(x, y, z) = (x, z, y)` applied at draw time only.
- True cross-section slicing: each object has a W-extent `[wMin, wMax]`; solid + collidable only when viewer W is inside, otherwise ghost wireframe (if defined) and no collision.

## 2. Constants
- Bounds: X ∈ [−12, 12], Y ∈ [−11, 11], W ∈ [−5, 5]. Spawn (−9, 0, 0, 0).
- Speeds: 4.0 u/s in X,Y; 2.5 u/s in W.
- Base-hyperplane slab: W ∈ [−0.05, +0.05] for thin planar objects.
- Tolerances: halt X = −0.5 ± 0.02 (wall half-thickness 0.2 + viewer radius 0.3); sidestep W₁ = 3.0 (triggers at W ≥ 2.75); bypass X ≥ 2.0 at W > 0.05; return |W| ≤ 0.05 at X > 0.5; continuation X ≥ 5.0 at W = 0.

## 3. Scene objects
1. Floor 26 × 24 at Z = 0, dim blue grid, W-extent unbounded.
2. Main path along X ∈ [−10, 10] at Y = 0, Z = 0.02, slab W-extent; solid amber in-slab, 35% dashed amber out.
3. Wall: 0.4 (X) × 24 (Y, ±12) × 3 (Z, on floor) at X = 0, slab W-extent. Solid crimson `#8d2f3f`; ghost = pinkish-red outline ≥ 75% + vertical hatch every 2 units in Y; ghost materials fog-exempt.
4. W₁ open-space sheet: 6% amber plane at X = 0, Y ± 12, Z ∈ [0, 3], W-extent [2.65, 3.35].
5. Landmark pillars r 0.35 × h 2.2: violet (−5, +4.5) W [−6, −1.5]; amber (+5, −4.5) W [+1.5, +6]; pale (+3, +5.5) W [−0.6, +0.6]. 22% wireframe ghosts.
6. Avatar r 0.3 pale sphere at Z = 0.3 with +X amber cone. 4D trail at Z = 0.06, vertex every 0.06 units, max 2400, ≥ 3px wide, W-colored (steel blue at 0 → amber +W / violet −W).

## 4. Atmosphere
- Void `#06080f`. Fog density `0.02 + 0.035·(|W|/5)`. Clear/fog color lerped by `0.9·(|W|/5)` toward warm amber (W > 0) or cool violet (W < 0).

## 5. Collision
1. 3D wall collision only when |W| ≤ 0.05 and Z < 3; entering X ∈ [−0.5, 0.5] halts X motion.
2. W-return block: inside wall footprint (|X| < 0.5) at |W| > 0.05, translating back into the slab is blocked at |W| = 0.051; contact "W-return blocked"; warning toast.
3. Snap: on W-key release with 0 < |W| < 0.09 → W = 0 if |X| ≥ 0.5, else clamp to ±0.051.

## 6. UI
1. Status card (top-left): hyperplane, position, W offset, wall state, contact, camera mode.
2. W gauge (right): 300px vertical Ana→Kata gradient, ticks at 0 and W₁, marker, signed readout.
3. Checklist (bottom-left): six steps, strict order.
4. X–W minimap (bottom-right): X ∈ [−12, 12] horizontal, W ∈ [−5, 5] vertical (+W up); path, wall rectangle at origin, W₁ line, viewer dot, trail; caption stating the bypass is non-intersecting and non-parallel — skew.
