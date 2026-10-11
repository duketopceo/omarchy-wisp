# Landscape: voice assistants and bar plugins with an on-screen presence

Date: 2026-10-10. Scope: what else exists for an Omarchy/Hyprland user who wants a voice companion that shows up on screen, what each does better or worse than omarchy-wisp, and what is unique here. Every claim carries a link. Things I could not confirm are marked **unverified**.

## What this repo is (from the code)

- A Quickshell plugin for `omarchy-shell` with three entry points in `manifest.json`: a headless service (`WispService.qml`), a bar widget (`BarWidget.qml`) and an overlay (`Companion.qml`), plus a panel (`Panel.qml`). 5,287 lines of QML and JS, one baked shader (`shaders/wisp.frag.qsb`).
- It renders state only. A separate daemon, `wispd` (in [duketopceo/wisp](https://github.com/duketopceo/wisp)), does speech, routing and actions and writes `$XDG_RUNTIME_DIR/wisp/state.json`; the plugin reads it. Without the daemon the plugin shows an offline state (`README.md`).
- It is a mirror of the monorepo's `shell-plugin/`, and is currently behind it (see the roadmap plan, Status Ledger).

## How Omarchy plugins work (constraints for this repo)

The [Omarchy develop guide](https://plugins.omarchy.org/develop.html) lists six plugin kinds (`bar-widget`, `panel`, `overlay`, `menu`, `service`, `bar`), required manifest fields, `omarchy plugin validate`, and a warning that plugins run unsandboxed in the shared shell process and must not start a second Quickshell process. It gives no implementation guidance for overlays or services. The marketplace at plugins.omarchy.org is community run; the [HANCORE-linux marketplace repo](https://github.com/HANCORE-linux/omarchy-plugin-marketplace) states it is not affiliated with Omarchy or 37signals. Install is `omarchy plugin add <repo> --enable` (same guide and the [starryhope install walkthrough](https://www.starryhope.com/linux/install-omarchy-plugins/)).

## Comparable projects

| Project | What it is | On-screen presence | Better than omarchy-wisp | Worse than omarchy-wisp |
|---|---|---|---|---|
| [Clicky](https://github.com/farzaa/clicky) (MIT, 7.7k stars) | macOS menu-bar AI teacher; push-to-talk, screenshot, spoken answer | Full-screen transparent overlay with a blue cursor that flies to elements named by `[POINT:x,y:label]` tags in the reply | Pointing is embedded in the reply, so speech and pointing stay in sync; polished, shipped product | macOS only; needs Anthropic, AssemblyAI and ElevenLabs keys; the author says new work is closed source |
| [end-4 dots-hyprland](https://github.com/end-4/dots-hyprland) (GPL-3.0, 16.3k stars) | Full Hyprland rice on Quickshell with an AI sidebar (Gemini, Ollama and others) | Sidebar chat, not a creature or cursor | Large audience; wallpaper-derived theming; settings app | The page does not mention voice; AI is a text sidebar; a whole-desktop install, not a plugin |
| [Noctalia shell](https://github.com/noctalia-dev/noctalia-shell) (MIT, 11k stars) | Wayland desktop shell with a plugin system (bar widgets, panels, services) | Bars, panels, OSDs | Mature plugin model; hot-reloaded TOML config | Its page does not mention voice or AI, and does not mention Quickshell (**unverified** how it is built); not Omarchy |
| [Voxtype](https://github.com/peteonrails/voxtype) (MIT, 1.6k stars) | Push-to-talk speech-to-text for Wayland compositors, Hyprland included | Status for the bar; text typed into the focused window via `wtype` or similar | Does one job (dictation) with several output backends | No assistant, no actions, no on-screen creature or cursor |
| [Handy](https://github.com/cjpais/Handy) (MIT, 33k stars) | Offline cross-platform speech-to-text app | A recording overlay | Large user base, runs offline | A third-party mirror reports the overlay can steal focus and break paste on Wayland (**unverified**, mirror not the official repo); dictation only |
| Wispr Flow, Superwhisper | Commercial dictation apps | A slim pill that expands on a hotkey; waveform only | Polished states; hover to reveal controls | Closed, paid, no Linux Hyprland story (cited through the wisp `DESIGN-v2.md` section 4.1: [Wispr Flow docs](https://docs.wisprflow.ai/articles/5096240724-navigating-the-wispr-flow-app-desktop-ios-and-android), [Superwhisper docs](https://superwhisper.com/docs/get-started/interface-rec-window)) |
| Apple Siri glow, ChatGPT voice orb | OS and app assistants | A light that reacts to voice amplitude; one soft blob carrying all states | The reference for "one living thing" | Closed; not available on Linux (via the wisp design doc: [9to5mac](https://9to5mac.com/2024/11/03/new-apple-intelligence-siri-looks-different-works-the-same/), [TechCrunch](https://techcrunch.com/2024/09/24/openai-rolls-out-advanced-voice-mode-with-more-voices-and-a-new-look)) |

I read the Clicky, end-4, Noctalia and develop-guide pages directly on 2026-10-10 and star counts from the GitHub API the same day. The Wispr, Superwhisper, Siri and ChatGPT rows rest on the wisp repo's own reference table, which I did not re-fetch.

## What users expect (from these products)

1. A visible, honest "mic is open" state on every surface (wisp `DESIGN-v2.md` section 7; Alexa-style paired start and end sounds).
2. Hotkey first, no window to focus. Handy's reported focus problem is the failure case to avoid; this plugin's overlays are click-through and take no keyboard focus (wisp Ember plan scope boundaries).
3. The assistant shows what it is doing and can be interrupted (Clicky's cursor flight; wisp's `Esc` while acting).
4. It looks like part of the desktop: end-4 and Noctalia both derive colour from the wallpaper or theme; Omarchy themes feed one `colors.toml` (wisp design doc).
5. Install in one command and say what else is required. Omarchy's guide asks authors to document dependencies and services.

## What is unique here

- **Presence is a plugin, not a rice or an app.** It drops into the Omarchy shell the user already runs, via the documented plugin kinds.
- **The creature is also the pointer.** The Ember design makes the light that listens the same light that travels to a target as the ghost cursor (wisp `DESIGN-v2.md` section 4.3). Clicky has a flying cursor but no persistent creature; the dictation tools have neither.
- **Theme-derived ember colour.** The accent is computed from the active Omarchy theme and is never an error colour (wisp `lib/tokens.js`, `DESIGN-v2.md` section 5.2).
- **Thin view over a daemon.** State flows one way from `state.json`, which keeps this repo small and lets the daemon change without QML changes, but makes the repo useless alone and introduces the sync problem the roadmap addresses.

## Weaknesses to address (feeds the roadmap)

- Distribution: hand-synced mirror already behind its source; no CI; stale README. (U1 to U3)
- Dependency honesty: the install requires a separate daemon pinned to a commit. (U5)
- Dead weight: about 395 lines of management-app views shipped inside the plugin. (U4)
- No live recording or screenshot of the real plugin in the README. (U10)
