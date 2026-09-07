# Ink — a study in diffusion

An interactive simulation of ink dispersing through water, rendered in real time on the GPU.

Open `index.html` in any modern browser. No build step, no dependencies. It runs from a local file, from a static host, or from a USB stick.

## Interacting

| Input | Effect |
| --- | --- |
| Click / tap | drop ink |
| Drag | stir, leaving a trail of ink |
| Shift + drag, or right-drag | stir without ink |
| Move the mouse | a faint disturbance in the water |

| Key | Effect |
| --- | --- |
| Space | pause / resume |
| C | dissolve everything |
| M | switch between *water* (luminous ink on dark water) and *paper* (pigment on paper) |
| P | next palette |
| 1 – 9, 0 | pick an ink, or `0` for a different ink each drop |
| S | save the current frame as a PNG |
| H | hide the interface |
| F | fullscreen |
| , | settings |
| ? | about |

The interface fades away after a few seconds when the mouse rests (never while it is over a control) and returns on any movement or key. Left alone, the page keeps dropping ink on its own: single drops, pairs of related inks, and little runs of drips, each seen falling from the top of the page first. Leave it alone for a minute or so and it starts to rain: a shower that builds, holds for half a minute and eases off, then returns now and then for as long as nobody touches the water. Turn these off under *Settings → Auto drops* and *Settings → Rain*.

## How it works

Everything happens in WebGL fragment shaders (`app.js`):

- **Velocity** lives on a coarse grid (256 px on the short side by default), the **ink** on a finer one (1024 px).
- Each frame: vorticity confinement adds curl, gravity pulls dense ink down, a slow divergence-free current keeps the water alive, a Jacobi solve enforces incompressibility, and both fields are advected semi-Lagrangian style. The ink uses a MacCormack correction so filaments stay crisp, then a small explicit diffusion step softens them.
- The ink texture stores premultiplied colour in RGB and concentration in alpha. *Paper* mode renders it with Beer–Lambert absorption (`paper · exp(−k · absorbance)`), so overlapping inks mix like real pigment; *water* mode renders it as light scattered from the side, with dense ink blocking its own glow.
- Post: soft bloom, a light vignette, a whisper of chromatic aberration, film grain, and procedural paper fibre.

A small console API is exposed for tinkering, e.g. `INK.drop(0.5, 0.6)`, `INK.drip(0.5, 0.6)`, `INK.rain()`, `INK.mode(1)`, `INK.config.curl = 30`.

## Files

- `index.html` — markup
- `style.css` — interface, typography, motion
- `app.js` — fluid solver, renderer, interaction

Type: Cormorant Garamond and IBM Plex Mono, loaded from Google Fonts with system fallbacks.
