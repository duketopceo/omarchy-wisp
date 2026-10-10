# Brand assets (proposal)

Status: proposal. The owner signs off logos.

These are **not a new brand**. The mark, wordmark and palette are the Wisp "Ember in the terminal" identity that `duketopceo/wisp` already ships in `assets/brand/` and `assets/icons/app/` (`DESIGN-v2.md` A2 and A7 there). This directory re-exports them for this repo with explicit colours, because a `currentColor` SVG shown through an `<img>` tag renders black and disappears on GitHub dark mode.

## Files

| File | Use |
|---|---|
| `logo.svg` | mark, for light backgrounds (flame `#b85f1f`, core `#f0a060`) |
| `logo-dark.svg` | mark, for dark backgrounds (flame `#e58a4b`, core `#ffaa6b`, the Vantablack ember) |
| `wordmark.svg` | "wisp" lettering, `currentColor` (inline use only) |
| `lockup.svg`, `lockup-dark.svg` | mark plus wordmark with fixed colours, for the README header |
| `social-card.svg` | 1280x640 GitHub social preview |
| `bar-sizes.svg` | the mark at 16, 24 and 36 px, dark and light, for legibility review |
| `preview/*.png` | resvg renders, optimised with oxipng |

## Construction

- Mark: a single closed path on a 16 unit grid, a teardrop whose tail sweeps up and to the right. The body is a circle of radius 4.5 centred at (6.5, 10); the tail leaves the circle at its upper right and ends in a point near (14, 1.75), so the light seems to move toward the upper right (the direction the Wisp leads). A circle of radius 2.1 at the body centre is the core. No gradients, no strokes, so it holds at 16 px.
- Wordmark: lowercase "wisp", 1.5 unit stroke, round caps and joins, hand-drawn as one path on a 32 by 18 grid. The dot of the "i" is a zero-length segment with a round cap.
- Lockup: the mark at scale 1 (offset 0.9 down) and the wordmark offset 17.5 right, so the x-height of the letters sits at the vertical centre of the body.
- Colours: `#e58a4b` and `#ffaa6b` are Vantablack's own orange; the shipped plugin derives its ember from the active Omarchy theme at run time (`lib/tokens.js`), so these fixed values are for static media only. The light variant darkens the flame to keep 4.3:1 or better against white and cream (checked: 4.47:1 on white, 4.35:1 on `#fffcf0`; the mark is non-text, needs 3:1).
- Wordmark contrast: `#1f1b17` on white 17.1:1; `#f2ece6` on `#14110f` 16.0:1.

No AI image generation and no stock icons were used. Provenance of the paths is the wisp repo (MIT, `assets/LICENSE` there).
