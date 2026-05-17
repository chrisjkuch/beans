---
title: "Add common-pitfalls section to beans prime agent instructions"
status: completed
type: task
---

A failure analysis of 307 agent-driven `beans` invocations surfaced five recurring traps (~16 of 16 failures attributable to these patterns). Add concise, targeted guidance to the `beans prime` agent instructions so every agent in every project sees the warnings — not just agents working on the beans project itself.

Initially scoped as a CLAUDE.md addition; corrected to `internal/commands/prompt.tmpl` after realizing the guidance is universal CLI usage rather than beans-project-specific.

## Summary of Changes

`internal/commands/prompt.tmpl` (+18 lines net):

- **Fixed the existing multi-replacement example** that demonstrated the very pattern that breaks (`beans query 'mutation { ... }'`). Replaced with a `cat <<'EOF' | beans query` stdin pattern, with a leading sentence calling out *why* the previous form fails.
- **Added a bullet about `bodyMod.replace` being exact-match** to the same section, pointing agents at `beans show <id> --body-only` when the on-disk form has drifted.
- **New `## Common pitfalls` section** (5 bullets, distilled from the failure clusters):
  - Multi-line GraphQL bodies in `'...'` break the shell parser
  - `beans update` has no `--body-replace-old/--body-replace-new` flags (those are GraphQL `bodyMod` fields, agents hallucinate them as flags)
  - `BeanFilter.parentId` (not `parent`), takes single `String` not `[String]`
  - `beans show <id>` errors hard on unknown IDs — list first
  - `beans list --json` returns a bare array, not `{ "beans": [...] }`
