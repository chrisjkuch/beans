---
# beans-8epu
title: 'TUI: blocked/implicit-status annotations push rows past terminal width, causing wraps'
status: in-progress
type: bug
priority: normal
created_at: 2026-05-16T14:57:59Z
updated_at: 2026-05-16T14:59:01Z
parent: beans-g9oy
---

## Problem

The TUI list delegate (`internal/tui/list.go` Render) calculates `maxTitleWidth = m.Width() - baseWidth` where `baseWidth` only accounts for cursor + ID + type + status + tags. The title is truncated to that width.

After the title, the renderer in `internal/ui/styles.go` appends:
- ` ↑<status>` for beans with an implicit (inherited) status
- ` ⊘` for beans with active blockers (added in beans-g9oy)

These suffixes are not subtracted from `maxTitleWidth`, so rows with these annotations overflow the terminal width and wrap to a new line.

Surfaced on the blocked-viz PR (parent: beans-g9oy) because `⊘` makes the overflow visible on a lot more rows than `↑status` did.

## Todo

- [x] In `internal/tui/list.go` Render, subtract `len(' ⊘')` from maxTitleWidth when `item.blocked`
- [x] Subtract `len(' ↑') + len(item.implicitStatus)` from maxTitleWidth when `item.implicitStatus != ''`
- [x] Confirm dimmed rows don't apply the suffix (renderer already gates on `!cfg.Dimmed`); width reservation should match that gate
- [ ] Build TUI and visually verify a long-title blocked bean no longer wraps

## Summary of Changes

In `internal/tui/list.go` Render, reserve space in `baseWidth` for the trailing `↑<implicitStatus>` and `⊘` annotations (gated on `item.matched`, matching the `!cfg.Dimmed` gate in `RenderBeanRow`). Title gets truncated tighter, so the row stays inside the terminal width and no longer wraps to a new line.

Uses `len([]rune(...))` for the multi-byte glyphs, matching the convention already used in `internal/ui/styles.go` for tree prefix width.

`go test ./internal/tui/... ./internal/ui/...` passes. Visual verification in the running TUI deferred to the user.

Note: the CLI tree path `internal/ui/tree.go RenderTree` has the same width calculation and likely the same overflow, but kept out of scope of this bean (TUI-specific) — file a follow-up if it's a problem in `beans list` too.
