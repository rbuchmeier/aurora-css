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

The rendering itself needs only a loop that stamps out the slice divs; the
rest of the JavaScript is the optional config panel below.

## Config modal

Press <kbd>c</kbd> to open the in-page config panel. Sliders update the ribbon
live: slice count, camera focal length, tail altitude, approach climb, and the
wave shapes. Three color checkboxes toggle the green body, blue fringe, and
pink tops independently (all off hides the aurora entirely — each gradient
stop's alpha is gated by a `--c-*` custom property, and the ambient glow
follows). <kbd>Enter</kbd> saves the values to `localStorage` (they survive
reloads), <kbd>Esc</kbd> reverts to whatever was active when the panel opened.
`index.html#config` deep-links to the open panel.

## Tuning

All knobs are numeric custom properties on `:root` (`--slices`, `--persp`,
`--edge-y`, `--climb`, `--x-amp`, `--x-freq`, `--rip-amp`, `--z-amp`) — the
slice CSS derives the tangent coefficients from them with `amp × freq × π/180`,
so changing a knob can never desync the slice orientations. The one hard rule:
keep `--z-amp` ≤ 180 (the modal's slider is capped there) so depth stays
monotonic and DOM order keeps matching depth order.
