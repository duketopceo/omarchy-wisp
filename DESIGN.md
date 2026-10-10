# omarchy-wisp design direction: Ember in the terminal

Status: proposal for this repo, restating an existing direction. The direction, tokens, creature and mark are owned by `duketopceo/wisp` (`DESIGN-v2.md`, `docs/plans/2026-10-02-2315-feat-wisp-ember-redesign-plan.md`, program roadmap merged in wisp PR #104). Where this file and `DESIGN-v2.md` disagree, `DESIGN-v2.md` wins and this file should be corrected. Nothing here changes plugin code.

Method: the `refero-design` research-first method (reference lock, decision ledger, anti-slop gate). The Refero MCP was not available in this session, so the references below are the ones `DESIGN-v2.md` section 4.1 already collected, plus the Omarchy plugin guide and the landscape note (`docs/research/2026-10-10-landscape.md`). Those marked "via wisp" were not re-fetched.

## 1. Direction

**Name: Ember in the terminal** (same name as upstream, on purpose).

All chrome is Omarchy chrome: the active theme's colours, Hyprland's corner rounding, the user's monospace font, hairline keylines, flat fills, no blur. Inside that frame lives one luminous, organic, round thing, the Wisp. It is the only element that glows, moves continuously or is round when the theme is square. It does not sit in a box; it leads. Memorable detail: the creature that listens is the same light that flies to a target as the ghost cursor.

## 2. Reference lock

```text
Primary direction: the Omarchy shell kit (qs.Commons Color/Style, qs.Ui) for all chrome,
  with the Alexa light-ring state grammar for the creature (via wisp).
Preserve:
  1. Chrome colours resolve from the active Omarchy theme.
  2. Chrome radius = Style.cornerRadius; 0 stays 0.
  3. Type = Style.font.family (monospace) at Style.font.* sizes.
  4. Flat surfaces, 1px keylines, no blur, no drop shadow.
  5. Creature states: hue = identity, tempo = urgency, cohesion and gaze = attention. Never hue alone.
Borrow only: Clicky's pointing-in-the-reply idea (answer at cursor, click-through overlays);
  M3 Expressive spring values as motion tokens (via wisp).
Role rules:
  ember = the Wisp, ghost cursor and mic-level meter ONLY (never a button, tab or border fill).
  fail (urgent) = failed only. needsYou = waiting on the user only. No glow on chrome.
Media strategy: code-native shader creature, hand-drawn vector icons, real recordings for README.
Reject: Tokyo Night hex as a brand, violet accents, glass or blur cards, 12px radius everywhere,
  infinite pulses, Unicode characters as icons, em-dashes in UI copy, spinner rings.
```

## 3. Decision ledger

| Decision | Source | Role rule | Why |
|---|---|---|---|
| Chrome colours from the Omarchy theme | wisp `DESIGN-v2.md` 5.1; Omarchy theming (one `colors.toml`) | theme owns chrome | the plugin must look installed, not pasted |
| Radius follows `Style.cornerRadius` | wisp finding 2 | chrome only | Vantablack is square |
| Creature is the only glowing, round, continuously moving thing | Alexa ring, ChatGPT orb (via wisp) | glow = creature | one focal point |
| Ember derived from the theme accent, never near the error colour | wisp `lib/tokens.js` (`EMBER_URGENT_DE = 10`) | calm never reads as failure | on Vantablack it yields the theme's own `#e58a4b` |
| Mark and wordmark reused from wisp | wisp `assets/brand/`; owner's "do not invent a new brand" instruction | one identity across repos | consistent distribution |
| Fixed-colour mark variants for light and dark READMEs | GitHub `<img>` SVG cannot inherit `currentColor` | static media only | the shipped plugin uses theme tokens |
| No infinite animation on chrome | Apple HIG motion, Lumiere (via wisp) | only the creature loops, only while a state needs it | calm when ignored |
| Overlays take no keyboard focus | Omarchy guide (shared shell process); Handy's reported focus problem | click-through except chips and bubble actions | must not break the user's typing |

## 4. Tokens

Runtime source of truth is `lib/tokens.js` (derive from `colors.toml` and `shell.toml`), tested upstream against `tests/fixtures/themes/*/expected.json`. The table shows two fixture themes with contrast ratios against `canvas` that I computed (WCAG relative luminance).

| Token | Role | Dark: Vantablack | Ratio | Light: Flexoki Light | Ratio |
|---|---|---|---|---|---|
| `canvas` | bubble, pill, panel fill | `#000000` | - | `#fffcf0` | - |
| `ink` | primary text | `#ffffff` | 21.0 | `#100f0f` | 18.62 |
| `inkMuted` | secondary text | `#837b72` | 5.04 | `#74736d` | 4.63 |
| `keyline` | 1px chrome border | `#666666` | 3.66 | `#9f9d96` | 2.64 |
| `ember` | the Wisp, ghost, mic meter | `#e58a4b` | 8.07 | `#205ea6` | 6.36 |
| `emberCore` / `emberWick` | creature core and tail | `#ffaa6b` / `#c36b29` | | `#3f7cc7` / `#004186` | |
| `needsYou` | awaiting the user | `#e5c07b` | 12.16 | `#b68900` | 3.11 |
| `fail` | error only | `#e06c75` | 6.57 | `#d14d41` | 4.21 |
| `ok` | brief success | `#98c379` | 10.42 | `#879a39` | 3.04 |

AA findings (upstream should fix; recorded here, not changed):
- Dark fixture passes AA text (4.5:1) for every text token. Keyline 3.66:1 passes the 3:1 non-text bar.
- Light fixture: `fail` 4.21:1 is below 4.5 as text, `needsYou` 3.11 and `ok` 3.04 pass only as non-text indicators (3:1) and must never carry text without an icon and word. Keyline 2.64:1 is decorative, so state must not depend on it alone (`DESIGN-v2.md` 7 already requires an `emberHalo` ring at 3:1).
- Static brand colours: see `assets/brand/README.md` (mark 4.47:1 on white; wordmark 17.1:1).

Type: `Style.font.family` (monospace alias), sizes `answer 13`, `body 12`, `label 11`, `caption 10`, `title 14` weight 600 (wisp 5.3). Heard transcript is not italic. Spacing from `Style.spacing.*` only.

## 5. Glyph and icon style

16 px grid, 1.5 px stroke, square caps and mitred joins, one weight, no duotone, delivered as `PathSvg` so tokens recolour them (wisp 5.6). The Wisp mark is the only rounded-cap glyph. Seven bar-mark states (idle outline; listening with ticks; thinking; acting with arrow tip; needs-you with notch; error with broken wick; offline dashed). Icon plus label in chips; icon alone only in the bar with a tooltip. Source SVGs live upstream in `assets/icons/src/`.

## 6. Motion rules

- Springs for travel (`spatialDefault`), 90 to 280 ms ease-out for colour and opacity (wisp 5.5).
- No `Animation.Infinite` on chrome. The creature loops only while listening, thinking or speaking; idle flicker is aperiodic and under 3 percent luminance.
- Reduced motion: brightness and hue only, travel becomes a 140 ms cross-fade; off: static mark per state. The `[ui] motion` key is not yet wired (roadmap U7).
- No flashing above 3 Hz; never colour alone.

## 7. Surfaces in this repo

| Surface | File | Design note |
|---|---|---|
| Bar mark and live pill | `BarWidget.qml`, `components/BarMark.qml` (and `BarPill.qml` after roadmap U1) | 14 px mark; busy underline in ember; tooltip with one line of context |
| Companion overlay | `Companion.qml` and components | creature, pill, bubble, ghost cursor, beacons; click-through, no keyboard focus |
| Panel | `Panel.qml`, `components/{NowTab,AgentsTab,SettingsTab,MemoryTab}.qml` | four tabs, accent underline for the selected tab, no filled pills |
| Service | `WispService.qml` | no visuals; owns tokens and the single state reader |
| README and social card | `README.md`, `assets/brand/social-card.svg` | lockup, real recording (TODO) |
| Not here | management app, TUI, arena | owned by the monorepo |

## 8. Anti-slop gate

Accent comes from the theme (no default indigo or violet): pass. Cards only where interaction lives: pass. No emoji or Unicode glyphs as icons: pass upstream (R7 there). No decorative status dots: pass. No decorative serif or word swap in the README: pass. Warm ember on near-black is Vantablack's own palette, not a chosen "calm editorial" look; on other themes the accent changes with the theme.
