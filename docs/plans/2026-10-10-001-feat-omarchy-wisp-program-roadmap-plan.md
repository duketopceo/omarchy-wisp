---
title: omarchy-wisp Program Roadmap - Plan
type: feat
date: 2026-10-10
artifact_contract: ce-unified-plan/v1
product_contract_source: repo-program-brief
execution: docs
origin: docs/research/2026-10-10-landscape.md
parent_plans:
  - duketopceo/wisp docs/plans/2026-10-05-001-feat-wisp-full-roadmap-plan.md
  - duketopceo/wisp docs/plans/2026-10-02-2315-feat-wisp-ember-redesign-plan.md
design: DESIGN.md
---

# omarchy-wisp Program Roadmap - Plan

## Goal Capsule

- **Objective:** make `duketopceo/omarchy-wisp` a trustworthy, installable, always-current distribution of the Wisp Omarchy shell plugin, wearing the Ember design that `duketopceo/wisp` already defines.
- **Means:** fix the distribution problem first (this repo is a hand-synced copy that is already behind its source), then add the checks that a plugin repo needs (validation, lint, compatibility), then carry the open Ember units that land in `shell-plugin/` into this repo through the same pipe.
- **Authority hierarchy:** user instructions > the wisp roadmap and Ember plans (they own product behaviour and unit IDs) > this plan (owns distribution, packaging and the repo's docs) > `DESIGN.md` (this repo's surface-level restatement of Ember).
- **Stop conditions:** stop and ask if (a) any unit here would change plugin behaviour that the monorepo has not changed first (the monorepo is the source of truth), (b) the sync automation would need to weaken default-branch protection on this public repo, (c) a unit needs a paid API call or generated imagery.
- **Docs-only pass.** This plan, `DESIGN.md`, `docs/research/2026-10-10-landscape.md`, `assets/brand/*` and `README.md` are the only changes in the PR that introduces it. No QML or JS is touched here.

## Status Ledger

Verified 2026-10-10 against `origin/main` at `3f2a97f` of this repo and `origin/master` at `197c1c3` of `duketopceo/wisp`.

| Fact | Evidence |
|---|---|
| This repo is a mirror of `wisp/shell-plugin/`, produced by hand | `README.md` says "published here via subtree split"; no workflow exists (this repo has no `.github/`); only two merged PRs (#1 root README and LICENSE, #2 pin note) |
| The mirror is behind its source | `wisp` PR #109 (bar live-activities pill replaces the corner orb) added `components/BarPill.qml` and removed the corner orb path; `diff` of `git archive origin/master shell-plugin` against this tree shows `BarPill.qml` missing here, and `Corner.qml`, `CornerLayer.qml`, `Console.qml` still present here but not upstream. `BarWidget.qml`, `Companion.qml`, `Panel.qml`, `OverlayLayer.qml`, `lib/companion.js` differ |
| `wisp` local clone `feat/agent-parity` also has `RunCard.qml` and `lib/run.js` | present in that branch, not yet on `master`; do not mirror from a feature branch |
| Plugin version is not moving | `manifest.json` `version` is `0.4.0` in both repos |
| README is stale | lists panel tabs Now/Agents/Activity/Telemetry/Skills/Context/Connect and "companion orb"; the shipped panel has four tabs (`Panel.qml` over `NowTab`, `AgentsTab`, `SettingsTab`, `MemoryTab`) |
| No checks of any kind | no `.github/workflows`, no tests (tests live in `wisp/tests/qml`), no manifest validation, no lint |
| Some files here are dead in the plugin | `components/{AuditView,BindsView,SpendView,HealthView}.qml` (299 lines) and `lib/manage.js` (96 lines) have no importer outside each other; they serve the monorepo's management app |
| A compatibility guard already exists | `lib/state.js` reads `contract_version` and refuses a newer daemon (`lib/state.js:11`, `:84`); nothing tells the user which wispd to install other than a pinned commit in `README.md` |
| Code size | 5,287 lines of QML and JS in 4 entry QMLs, 31 components, 16 libs, one baked shader |

## Which wisp units this repo implements

This repo ships the QML surfaces of Wisp. The behaviour is specified and built in the monorepo; this repo is the delivery channel plus its own packaging work.

| wisp unit | What lands in omarchy-wisp | This plan |
|---|---|---|
| Ember U7 (creature shader) | `shaders/wisp.frag.qsb`, `components/Creature.qml`, `lib/creature.js` | shipped; verified by U3 |
| Ember U10 (bar mark), U17 (panel, four tabs) | `components/BarMark.qml`, `BarWidget.qml`, `Panel.qml`, tabs | shipped; U1 re-syncs the post-#109 state |
| Ember U11, U12, U13 (corner, pill, bubble, ghost cursor, beacons) | `components/{Pill,Bubble,Answer,GhostCursor,Beacon}.qml`, `Companion.qml` | shipped, with U13's pointer fork gap open (U8 here) |
| Ember U2 gap (`[ui] motion`) | `WispService.qml` `motionConfig` | U7 here |
| Ember U8 (earcons) | `SoundEffect` in the plugin plus `assets/sound/` | U9 here |
| Ember U13 (pointer without forks) | `Companion.qml` `cursorProc` | U8 here |
| Ember U20 (README recordings, multi-theme, perf evidence) | README media, snapshots | U10, U11 here |
| wisp roadmap U1 (v1.0 gate, tag, marketplace verify request) | tags and the listing for this plugin | U6 here |
| wisp roadmap U5 (Ember UI finish) | all of the above | U7 to U11 here |
| wisp roadmap U10 (packaging and distribution) | marketplace listing, release notes | U6 here |
| wisp roadmap U11 (Ember ceiling) | panel morph, multi-monitor ghost cursor | U12 here |

Units that do not touch this repo: wisp roadmap U2 to U4, U6 to U9, U12 (daemon, spend, CUA, training, STT, contract freeze, adapters).

## Product Contract

### Summary

A user runs `omarchy plugin add https://github.com/duketopceo/omarchy-wisp.git --enable` and gets the same UI that the monorepo's `shell-plugin/` ships today, shown through the Ember design (theme-derived colours, one living creature, owned vector icons). The repo says plainly what it needs (wispd), which version, and what it shows when wispd is missing or too old.

### Requirements

- R1. `main` always equals a named commit of `wisp` `master` `shell-plugin/`, with that commit recorded in the tree.
- R2. Sync is automated; humans do not copy files.
- R3. Every change to this repo passes `omarchy plugin validate` and `qmllint` against the Omarchy shell imports.
- R4. The manifest `version` follows the wisp release that the plugin matches, and the README names the compatible wispd range.
- R5. A missing, stopped or too-old wispd produces a designed offline state (already specified by Ember U16), and this repo tests that it renders.
- R6. Brand and design docs here restate Ember; they never fork it (`DESIGN.md` section 1).
- R7. Default-branch protection (PR plus one review, no force push) stays on, per the owner's repo security standard (`AGENTS.md` in the personal workspace).

### Scope Boundaries

- No plugin behaviour change originates here. Issues and PRs about behaviour go to the monorepo.
- No second copy of `wisp/tests/qml`; this repo runs lint and validation, and a render smoke test only if cheap (U3).
- No new brand: the mark, wordmark and tokens are Ember's (`assets/brand/README.md` records provenance).

### Success Criteria

- A `git diff` between this repo and the recorded upstream commit's `shell-plugin/` is empty.
- A fresh Omarchy 4 install can add the plugin from the README command, and `omarchy plugin list --json` shows it enabled.
- Merging a plugin change in the monorepo produces a PR here within minutes, without a human.

## Planning Contract

### Key Technical Decisions

- **KTD1. The monorepo is the only place plugin code is edited.** Reason: two homes guarantee drift, which has already happened (Status Ledger).
- **KTD2. Sync by pull request, not direct push.** Reason: R7. A bot opens a PR from a `sync/wisp-<sha>` branch; protection requires one review, which the owner gives in seconds for a mechanical PR, or the owner scopes a narrow ruleset bypass for the sync bot only. Decide in U2.
- **KTD3. Record the source commit in a plain file** (`.wisp-source`: one line, the wisp commit SHA and the split date). Reason: makes R1 checkable by script.
- **KTD4. Checks use the Omarchy tools, not custom scripts.** `omarchy plugin validate` and `qmllint -I "$OMARCHY_PATH/shell"` are the documented validators ([Omarchy develop guide](https://plugins.omarchy.org/develop.html)).

### Risks

| Risk | Mitigation |
|---|---|
| CI runner has no Omarchy shell imports for `qmllint` | run lint in an Arch container with `omarchy` installed, or lint only syntax (`qmllint` without `-I`, accept unresolved-import warnings) and record the choice in U3 |
| Bot PR sync collides with the public-repo protection standard | KTD2 and U2 verification |
| wispd v0.9.0 pin (`README.md`) predates the UI contract the mirror now speaks | U5 makes the compatible range explicit and testable |
| Unit work lands in the monorepo and in this repo's README at different times | README links the roadmap and names the source commit via `.wisp-source` |

## Implementation Units

Stable U-IDs. Status is as of 2026-10-10. Difficulty is easy, medium or hard with one line of why.

### Unit Index

| U-ID | Horizon | Title | wisp unit | Depends on | Status |
|---|---|---|---|---|---|
| U1 | Now | Re-sync to `wisp` `master` and record the source SHA | roadmap U5 | none | not started |
| U2 | Now | Automate the sync (monorepo workflow opens a PR here) | roadmap U10 | U1 | not started |
| U3 | Now | CI: validate, lint, no-symlink, shader hash | roadmap U10 | U1 | not started |
| U4 | Now | Drop files the plugin does not load | none | U1 | not started |
| U5 | Next | Compatibility range: manifest, README, offline state test | Ember U16 | U2 | not started |
| U6 | Next | Version and tag policy, marketplace verification request | roadmap U1, U10 | U2, U3, U5 | not started |
| U7 | Next | Carry the `[ui] motion` wiring (Ember U2 gap) | Ember U2 | wisp-side change | open upstream |
| U8 | Next | Carry the pointer-without-forks change (Ember U13) | Ember U13 | wisp-side change | open upstream |
| U9 | Next | Carry the earcons (Ember U8) | Ember U8 | wisp-side change | not started upstream |
| U10 | Next | README media from the real plugin (Ember U20) | Ember U20 | U6, user at the desktop | TODO marker in README |
| U11 | Later | Multi-theme render check in CI | Ember U20 | U3 | not started |
| U12 | Later | Ember ceiling items that touch the plugin | roadmap U11 | U5 upstream | not started |

### U1. Re-sync to `wisp` `master` and record the source SHA

- **Status:** not started. **Evidence:** Status Ledger row 2.
- **Approach:** take `git archive origin/master shell-plugin` of the monorepo at a named merge commit, replace the tree (keeping `LICENSE`, `README.md`, `assets/`, `docs/`, `.github/`), add `.wisp-source`. This removes `Corner.qml`, `CornerLayer.qml`, `Console.qml` and adds `BarPill.qml`, matching upstream #109.
- **Dependencies:** none.
- **Verification:** `diff -r` against the archive of the recorded commit is empty apart from the kept files; `omarchy plugin validate .` exits 0 on a machine with Omarchy.
- **Difficulty:** easy. One mechanical copy, one decision about which commit.
- **Feasibility:** blocked only if the wanted commit is on an unmerged branch (the local clone is on `feat/agent-parity`, which holds `RunCard.qml`); use `origin/master`.
- **Simpler alternative:** none smaller; the fix for needing U1 at all is U2.

### U2. Automate the sync

- **Status:** not started. **Evidence:** README claims a subtree split; the monorepo's workflows are `argus.yml`, `release.yml`, `test.yml` and none touch this repo.
- **Approach:** a workflow in `duketopceo/wisp` that, on push to `master` touching `shell-plugin/**`, runs `git subtree split --prefix=shell-plugin` or an archive export, writes `.wisp-source`, and opens or updates a PR in this repo from `sync/wisp-<sha>`. Auth by a fine-grained token or a deploy key stored as a repo secret (kept out of logs).
- **Dependencies:** U1 (a clean baseline so the first bot PR is small).
- **Verification:** a trivial `shell-plugin/` edit in the monorepo produces a PR here with the expected diff; `.wisp-source` equals the commit; the PR cannot be merged without the review the protection rule requires (R7).
- **Difficulty:** medium. The git part is small; the cross-repo credential and the protection rule are the care points.
- **Feasibility:** a token that can write PRs to this repo; the owner's rule (PR plus one review) means each sync PR needs approval unless a narrow bypass is granted. Marketplace: unknown whether `omarchy plugin add` accepts a repo subdirectory (the [develop guide](https://plugins.omarchy.org/develop.html) does not say); check before building.
- **Simpler alternative:** if `omarchy plugin add` can install from a subdirectory or a `manifest.json` path, delete this repo's duplicated tree and point the listing at `wisp/shell-plugin`. That removes the mirror entirely (all 5,287 lines of QML and JS here, plus U1, U2 and `.wisp-source`). Prove or refute this first; it is a one-hour experiment.

### U3. CI: validate, lint, no-symlink, shader hash

- **Status:** not started. **Evidence:** no `.github/` directory.
- **Approach:** one workflow, three jobs: `omarchy plugin validate` (manifest, kind versus entry point, safe paths, no symlinks); `qmllint` over `*.qml` and `components/*.qml`; a check that `shaders/wisp.frag.qsb` is unchanged from the monorepo's recorded `assets/shaders/wisp.frag.sha256` (do not rebake here).
- **Dependencies:** U1.
- **Verification:** workflow green on `main`; an intentionally broken manifest in a throwaway branch turns it red. Record each exit code; never judge through `| tail`.
- **Difficulty:** medium. Writing the YAML is easy; getting Omarchy's shell imports into a runner is the work.
- **Feasibility:** `qmllint` is not installed on the author's machine (`which qmllint` found nothing on 2026-10-10); Qt tooling and `$OMARCHY_PATH/shell` must come from an Arch container image. If the imports are unavailable, lint syntax only and say so in the workflow.
- **Simpler alternative:** run only `omarchy plugin validate` plus `qmllint` without `-I`. Most of the value (manifest and syntax errors) for a fraction of the CI setup.

### U4. Drop files the plugin does not load

- **Status:** not started. **Evidence:** Status Ledger row 7 (`components/AuditView.qml` 71, `BindsView.qml` 94, `SpendView.qml` 112, `HealthView.qml` 22, `lib/manage.js` 96 lines, no importer in the plugin).
- **Approach:** these are management-app views. Upstream, move them under `shells/debug/` (or exclude them from the export in U2) so the exported plugin carries only what `manifest.json` entry points reach. Do not delete them here by hand (KTD1).
- **Dependencies:** U1, and a monorepo change.
- **Verification:** `grep` for each file name finds no importer in the exported tree; the plugin loads in a dev shell (`omarchy-shell shell summon` per the develop guide) with the panel and bar mark intact.
- **Difficulty:** easy. Pure file moves, once the monorepo agrees the management app owns them.
- **Feasibility:** `HealthSection.qml` is shared (used by `SettingsTab`); keep it. `FirstRunCard.qml` imports `lib/onboard.js`; keep both.
- **Simpler alternative:** this unit is the simplification (about 395 lines out of 5,287).

### U5. Compatibility range

- **Status:** not started. **Evidence:** `lib/state.js` rejects a daemon whose `contract_version` is newer than the reader's; `README.md` pins wispd to one commit.
- **Approach:** state in `README.md` and in a `docs/COMPATIBILITY.md` stanza which wispd versions the plugin version speaks; make the offline and "update wispd" copy come from the shared copy table; render both states in a snapshot or a manual script.
- **Dependencies:** U2.
- **Verification:** run the plugin against a state file with `contract_version` one above and one below the reader; each shows its designed state.
- **Difficulty:** medium. The reader already does the work; the effort is deciding the supported range and testing both edges.
- **Feasibility:** needs the monorepo's IPC contract (`docs/IPC_CONTRACT.md`) to stay additive (the Ember plan resolved it so).
- **Simpler alternative:** keep the README pin and add one sentence per release; skip the snapshot test.

### U6. Version and tag policy, marketplace verification request

- **Status:** not started. **Evidence:** both manifests are `0.4.0`; wisp roadmap U1 gates `v1.0.0` and the "marketplace verify request"; wisp roadmap U10 owns packaging.
- **Approach:** the plugin tag equals the wisp tag it was cut from (`v0.9.x`, then `v1.0.0`); `manifest.json` `version` follows. After wisp roadmap U1 lands, file the listing request on the marketplace ([plugins.omarchy.org](https://plugins.omarchy.org/develop.html)).
- **Dependencies:** U2, U3, U5.
- **Verification:** a tag exists, release notes link the wisp release, `omarchy plugin list --json` shows the version.
- **Difficulty:** easy once U2 to U5 exist; the policy is one paragraph.
- **Feasibility:** the marketplace's verification process is not documented in the guide fetched on 2026-10-10; ask before promising anything in the README. Marketplace is community run and not affiliated with Omarchy per its own repo description (search result for [HANCORE-linux/omarchy-plugin-marketplace](https://github.com/HANCORE-linux/omarchy-plugin-marketplace)).
- **Simpler alternative:** skip a listing; keep the `plugin add <url>` path only.

### U7. Carry the `[ui] motion` wiring

- **Status:** open upstream (Ember U2 gap: `WispService.qml` reads `motionConfig`; nothing sets it; `animationsEnabled` never read from Hyprland).
- **Approach:** implemented in the monorepo; arrives via U2. This plan's job is verifying `motion = reduced` and `off` in the exported plugin.
- **Verification:** with `[ui] motion = "off"` the creature is static and no `Animation` runs on chrome; recorded in the PR description.
- **Difficulty:** medium. Small code, but needs a config key, Hyprland option read and a three-mode manual check.
- **Feasibility:** needs `hyprctl getoption animations:enabled` or the Quickshell Hyprland module; the former forks a process once at load, which is acceptable.
- **Simpler alternative:** read the key only; skip the Hyprland default and document `reduced` as the recommended setting on low-power machines.

### U8. Carry the pointer-without-forks change

- **Status:** open upstream (Ember U13: one `hyprctl cursorpos` fork per answer in `Companion.qml` `cursorProc`).
- **Approach:** upstream moves to the Hyprland event socket or a single persistent reader; arrives via U2.
- **Verification:** `strace -f -c` or a process count over a scripted turn shows zero `hyprctl` forks.
- **Difficulty:** hard. Pointer position is not an event on Hyprland's socket2; whether Quickshell exposes it is unverified.
- **Feasibility:** may be impossible without a polling reader; upstream decides.
- **Simpler alternative:** keep one fork per answer. It is about one process per spoken answer, not the 11 per second the old ring loop cost (`DESIGN-v2.md` finding 7). Treat this unit as optional.

### U9. Carry the earcons

- **Status:** not started upstream (Ember U8).
- **Approach:** five short WAVs and `SoundEffect` in the plugin; the files live in the monorepo's `assets/sound/` and ship with the export.
- **Verification:** mic-open and mic-close fire on a real turn; `[ui.sound]` off silences them.
- **Difficulty:** medium. Authoring is scripted, the open questions are defaults and latency.
- **Feasibility:** needs `QtMultimedia` in the Omarchy shell's Qt build (Ember plan notes `qt6-multimedia` installed on the author's machine; not confirmed for other machines). Audio assets increase the plugin size.
- **Simpler alternative:** only the mic-open and mic-close pair (the Ember design's two required cues).

### U10. README media from the real plugin

- **Status:** `README.md` carries a marked TODO in place of a recording.
- **Approach:** a 6 to 8 second capture of Super+D, the pill, the creature, the bubble and the ghost cursor on two themes, from the running plugin. The monorepo's `assets/readme/*.png` are offscreen renders, not live captures, and are not used as screenshots here.
- **Verification:** the files embed in the README and are under 2 MB.
- **Difficulty:** medium. Needs a person at the desktop and a theme switch.
- **Feasibility:** only the owner runs the live theme switch (Ember plan, blocked on the user).
- **Simpler alternative:** one still from `grim` on the author's current theme.

### U11. Multi-theme render check in CI

- **Status:** not started. **Evidence:** the monorepo has `scripts/ui/snap.py` and fixtures for seven themes.
- **Approach:** run the monorepo's snapshot check against the exported tree as a scheduled job, not per PR.
- **Verification:** job green on the seven fixture themes.
- **Difficulty:** hard. Needs a Qt offscreen runner and Pillow in CI.
- **Feasibility:** the monorepo already depends on this; its CI status decides.
- **Simpler alternative:** rely on the monorepo's snapshot check, since the exported tree is byte-identical after U2 (R1). Then this unit is unnecessary.

### U12. Ember ceiling items that touch the plugin

- **Status:** not started (wisp roadmap U11).
- **Approach:** panel morph from the bar mark, multi-monitor hand-off for the ghost cursor, the lead behaviour. All upstream.
- **Verification:** per the wisp plan.
- **Difficulty:** hard. Multi-output overlays and spring tuning.
- **Feasibility:** one overlay per output in Quickshell layer-shell; Omarchy shell's single-process constraint (plugins must not start a second Quickshell process, [develop guide](https://plugins.omarchy.org/develop.html)).
- **Simpler alternative:** skip morphs; keep multi-monitor ghost travel only.

## Verification Contract

| Check | Command | Unit |
|---|---|---|
| Source equality | `git archive <sha> shell-plugin \| tar -x` then `diff -r` against this tree | U1, U2 |
| Manifest and layout | `omarchy plugin validate .` | U3 |
| QML lint | `qmllint -I "$OMARCHY_PATH/shell" *.qml components/*.qml` | U3 |
| Live load | `omarchy plugin list --json`, `omarchy-shell shell rescanPlugins` | U1, U6 |
| Docs lint (this pass) | `git diff --check`; every relative link in `README.md` and `DESIGN.md` resolves | this PR |

## Definition of Done

- The tree equals a recorded wisp commit and a bot keeps it so (U1, U2).
- CI is green and required on `main` (U3).
- README names the compatible wispd range and links this plan (U5).
- A tag and, if the owner wants it, a marketplace listing exist (U6).

## Sources

- `duketopceo/wisp` `DESIGN-v2.md`, `docs/plans/2026-10-02-2315-feat-wisp-ember-redesign-plan.md`, `docs/plans/2026-10-05-001-feat-wisp-full-roadmap-plan.md` (PR #104).
- [Omarchy plugin develop guide](https://plugins.omarchy.org/develop.html).
- `docs/research/2026-10-10-landscape.md`.

## Document review

Non-interactive `ce-doc-review` was not run in this pass (pipeline run without a reviewer available); the plan was self-checked for unit IDs, dependencies and per-unit Difficulty, Feasibility and Simpler alternative fields.
