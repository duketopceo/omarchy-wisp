# Roadmap

## Plans

1. [docs/plans/2026-10-10-001-feat-omarchy-wisp-program-roadmap-plan.md](docs/plans/2026-10-10-001-feat-omarchy-wisp-program-roadmap-plan.md) (2026-10-10): program roadmap.

| Horizon | Unit | Difficulty |
|---|---|---|
| Now | U1 re-sync to `wisp` master and record the source SHA | easy |
| Now | U2 automate the sync (monorepo workflow opens a PR here) | medium |
| Now | U3 CI: `omarchy plugin validate`, `qmllint` | medium |
| Now | U4 drop management-app files the plugin never loads | easy |
| Next | U5 compatibility range for wispd | medium |
| Next | U6 version and tag policy, marketplace request | easy |
| Next | U7 to U9 carry the Ember motion, pointer and earcon work from the monorepo | medium, hard, medium |
| Next | U10 README recording from the real plugin | medium |
| Later | U11 multi-theme render check, U12 Ember ceiling items | hard |

Behaviour is specified in [duketopceo/wisp](https://github.com/duketopceo/wisp) (Ember plan and roadmap plan); this repo carries the distribution work and the units listed in the plan's "Which wisp units this repo implements" table.
