# aurora-css

A static aurora borealis ribbon rendered (almost) entirely in CSS — no canvas,
no SVG, no images.

Open `index.html` in a browser.

## How it works

The ribbon is ~150 thin vertical "curtain slice" divs. Each slice:

- computes its position along a serpentine 3D path **in pure CSS**, using
  `calc()` with `sin()`, `cos()`, and `atan2()` driven by one inline `--i` index;
- projects itself with its own `perspective()` transform, so the far end
  genuinely shrinks toward a vanishing point on the horizon;
- is aimed along the path's analytic tangent via `rotateY(atan2(...))`,
  folded into ±90° with `mod()` so return legs of the snake get the correct
  slant, and clamped so fold apexes stack into soft bright columns;
- draws the aurora look with a vertical gradient (blue fringe → bright green
  edge → pink fade), horizontal mask feathering so slices melt
  together, pseudo-random ray heights, and distance haze via opacity.

Depth (z) increases monotonically along the path, so DOM order equals depth
order — plain painter's-algorithm layering with zero 3D plane-splitting
artifacts. The bottom edge also climbs the sky monotonically as it approaches,
so the bright lower line stays unobstructed along the entire wave.

The only JavaScript is a five-line loop that stamps out the slice divs.

## Tuning

All knobs live in the stylesheet:

- `--slices`, `--persp`, `--edge-y` on `:root`;
- the wave shapes in `--x` / `--z` / `--y` (keep the `--z` wave amplitude
  under ~181 so depth stays monotonic, and scale the matching `cos` terms in
  `--dx` / `--dz` by amplitude × frequency-in-radians when you change a wave).
