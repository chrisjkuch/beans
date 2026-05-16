---
# beans-jsud
title: 'TUI: hide archive-status beans by default with toggle to show all'
status: completed
type: feature
priority: normal
created_at: 2026-05-16T13:53:14Z
updated_at: 2026-05-16T13:58:46Z
---

Hide archive-status (completed/scrapped) beans by default in the TUI, with an `a` keybind to toggle "show all".

Builds on the mechanics from upstream PR hmans/beans#76 (matleh) but flips the default to hide-by-default and derives the status list from `Archive: true` rather than hardcoding `["completed","scrapped"]`.

## Todo

- [x] Add `showAll bool` (default `false`) to `listModel` in `internal/tui/list.go`
- [x] Helper `ArchiveStatusNames()` on `*Config`
- [x] `buildFilter()` composes `ExcludeStatus` (from archive statuses) with the existing tag filter
- [x] Add `a` case to the key switch — toggle + reload
- [x] Title via `buildTitle()`: `Beans`, `Beans [all]`, composes with `[tag: x]`
- [x] Help overlay entry for `a`
- [x] Footer help line entry for `a` (dynamic label: show all / hide done)
- [x] Unit tests for `buildFilter` (4 cases) and `buildTitle` (4 cases); plus `ArchiveStatusNames` test
- [x] `go test ./...` (excluding internal/web embed) green
- [~] Manual TUI test not run in this non-interactive session; binary compiles, help overlay entry verified
- [x] Commit + push branch to chrisjkuch/beans

## Out of scope

- Filter modal (#41) — separate larger effort
- Agent-env-var-based default (#73) — orthogonal idea
- Two-column ViewConstrained title update — yes, both title sites need updating


## Summary of Changes

- Added `(*Config).ArchiveStatusNames()` helper deriving the list from `StatusConfig.Archive: true` (not hardcoded), so future status-config additions flow through automatically.
- Added `showAll bool` to `listModel`. Default `false` => archive-status beans hidden on launch.
- Refactored title logic into `buildTitle()` and filter logic into `buildFilter()` for testability; both used from `View()`, `ViewConstrained()`, and `loadBeans()`.
- Added `a` keybind that toggles `showAll` and reloads. The footer label flips between `show all` (when hidden) and `hide done` (when shown).
- Help overlay shortcut added.
- 2 new unit-test functions in `internal/tui/list_test.go` covering both helpers under every relevant combo, plus a `TestArchiveStatusNames` in `pkg/config/config_test.go`.

Diverges from upstream PR hmans/beans#76 in three ways:
1. Default flipped to hide-on-launch (the original behavior was opt-in to hide).
2. Status set sourced from `ArchiveStatusNames()` rather than hardcoded `["completed","scrapped"]`.
3. Keybind `a` (semantically "show all") instead of `H` (which would mean "unhide" under the new default).
