---
# beans-df4f
title: Document parent-type hierarchy constraint in beans prime pitfalls
status: completed
type: task
priority: normal
created_at: 2026-05-17T20:15:10Z
updated_at: 2026-05-17T20:15:40Z
---

Add a 'Common pitfalls' entry to internal/commands/prompt.tmpl explaining the parent-type hierarchy (milestone > epic > feature > task/bug) and the error 'feature beans can only have milestone or epic as parent, not feature'. Recommend using --blocked-by for dependencies between same-type beans within an epic (similar to existing implicit-blocking guidance).

## Summary of Changes

Added a new bullet to the Common pitfalls section in `internal/commands/prompt.tmpl`:

- Documents the strict parent-type hierarchy (`milestone > epic > feature > task/bug`).
- Quotes the actual error string (`feature beans can only have milestone or epic as parent, not feature`) so agents can match it when triaging.
- Recommends `--blocked-by` for same-type dependencies, noting that `ready`/`next`/`start` honor blocking chains the same way they honor parent hierarchy.

Verified the hierarchy claim against `ValidParentTypes` in `pkg/beancore/links.go:446` — the chain is strict (each level only accepts strictly-higher types as parents). `go build ./internal/commands/` passes.
