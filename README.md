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

## Config modals

Press <kbd>c</kbd> for the aurora panel or <kbd>s</kbd> for the sky panel.
Sliders update the scene live. The aurora panel drives the ribbon: slice
count, camera focal length, tail altitude, approach climb, and the wave
shapes. Three color checkboxes toggle the green body, blue fringe, and
pink tops. The ribbon's shape never changes — the gradient's alpha profile is
fixed, and each stop's RGB is a priority-weighted blend of whichever colors
are on (each stop falls back to the next color in its chain, so e.g. blue and
pink meet where green used to be, and green alone fills the whole curtain).
All three off hides the aurora entirely.

The sky panel drives everything else: moon phase (0–200: invisible new moon
at both ends, full at 100 — waxing lights the right side, waning the left,
with true phase geometry: the terminator is an ellipse whose width follows
|cos| of the phase, carved out of a lit half-disc during crescents and added
to it during gibbous phases), moon size (75–200%), twilight level (a dusk
gradient rising from the horizon — at 0 the sky is a single flat night color
top to bottom; stars dim slightly as it brightens), star brightness and
density, and a mountains on/off toggle.

Both panels share the same lifecycle: <kbd>Enter</kbd> saves the values to
`localStorage` (they survive reloads), <kbd>Esc</kbd> reverts to whatever was
active when the panel opened. `index.html#config` and `index.html#sky`
deep-link to the open panels.

## Tuning

All knobs are numeric custom properties on `:root` (`--slices`, `--persp`,
`--edge-y`, `--climb`, `--x-amp`, `--x-freq`, `--rip-amp`, `--z-amp`) — the
slice CSS derives the tangent coefficients from them with `amp × freq × π/180`,
so changing a knob can never desync the slice orientations. The one hard rule:
keep `--z-amp` ≤ 180 (the modal's slider is capped there) so depth stays
monotonic and DOM order keeps matching depth order.
