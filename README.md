# Wisp — Omarchy shell plugin

Voice companion for the Omarchy bar: companion orb with mode badge,
cursor-adjacent answer bubble, listening pill, ghost cursor, and a full
panel (Now / Agents / Activity / Telemetry / Skills / Context / Connect).

This repository contains **only the Quickshell plugin**. It renders the
live state of the **wispd** daemon — without wispd running it shows a
disconnected/empty state.

## Requires

- **wispd** — the voice pipeline daemon (push-to-talk → whisper.cpp →
  Jev routing → tools/agents). Install it first:
  <https://github.com/duketopceo/wisp>

  ```sh
  git clone https://github.com/duketopceo/wisp
  cd wisp && python3 wispd install
  systemctl --user enable --now wispd
  ```

## Install

```sh
omarchy plugin add https://github.com/duketopceo/omarchy-wisp.git --enable
```

Then press `Super+D` (bind installed by `wispd install`).

## Remove

```sh
omarchy plugin remove io.github.duketopceo.wisp
```

## Development

The plugin is authored inside the monorepo at
<https://github.com/duketopceo/wisp> (`shell-plugin/`) and published here
via subtree split. Please open issues and PRs on the monorepo.

## License

MIT — see [LICENSE](LICENSE).
