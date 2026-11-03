---
name: audit
description: Use when checking how well a repo is set up for Claude to work in, or before changing that setup. Read-only.
context: fork
agent: Plan
background: false
---

Audit how this repo is set up for Claude, and change nothing. Focus: $ARGUMENTS (if empty, audit everything).

1. Read `${CLAUDE_PLUGIN_ROOT}/references/surfaces.md` and `${CLAUDE_PLUGIN_ROOT}/references/verification.md`.
2. Survey every surface at every scope that reaches this repo. The instruction files aren't in your context, so read them and follow their imports. Assess verification from what's recorded and from the build config. Launching the app is slow and writes files, so run nothing.
3. Flag anything that is:
   - misplaced (on the wrong surface)
   - at the wrong scope
   - stale (points to files, commands, or tools that are gone)
   - conflicting (two places disagree about the same behaviour)
   - dead weight (restates the default or what the code shows)
   - a verification gap

   For an owned surface, report only the owner and any conflicts.

The audit is done when every surface, and verification, has a status.

```markdown
## Findings
1. **<surface>** · `<path>` · <problem and why it matters>. Suggest: <change, target surface, scope>.

## Status
| Surface | Status |
|---|---|
| <name> | OK · <n> findings · absent, not needed · absent, worth adding · owned by <owner> |
| Verification | <status from verification.md> |

## Couldn't tell
- <what, and what would settle it>
```

Put the findings that would most improve Claude's work in this repo first. Omit empty sections.
