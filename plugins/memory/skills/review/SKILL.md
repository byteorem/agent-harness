---
name: review
description: Review the project's memory store and verify, merge, purge, or promote memories into more durable homes such as CLAUDE.md.
disable-model-invocation: true
---

Review `.claude/memory/` at the repo root: the `MEMORY.md` index and the memory files it points to. Memories are provisional, personal notes. The goal is a short index of live memories, with everything that has proven durable moved to a home where it does more good.

Memory files use the format the `memory:remember` skill defines: frontmatter with `name`, `description`, `type`, and `updated`, then the content.

## 1. Gather

Read the index and every file in the directory. Then read what's already in force, so you can spot duplicates and conflicts: the project's instruction files, following imports to the files that hold the content, `.claude/rules/`, `~/.claude/CLAUDE.md`, and the project's skills and hooks.

## 2. Verify

- Check the index against the directory. Every line should point to an existing file, and every file should have a line.
- Check each memory that names a file, function, command, or tool against the current code, config, and `git log`. Weigh the `updated` date: the older the memory, the harder you check it.
- Mark each memory as holding or not.

## 3. Plan

Give every memory exactly one action:

| Action | When |
|---|---|
| Purge | Wrong, stale, readable from the code, already covered elsewhere, or finished work |
| Merge | Overlaps another memory; name the survivor |
| Rewrite | Vague, missing its **Why:**, has an index hook that no longer matches it, or only makes sense with old session context |
| Promote | Stable and reusable: a rule that has held across sessions and applies beyond one task |
| Keep | None of the above |

Choose the promotion target by what the memory is:

| It's | Promote to |
|---|---|
| A project convention | The project instruction file, or a path-scoped `.claude/rules/` file if it applies to only part of the codebase |
| A `user` or `feedback` preference that holds in every project | `~/.claude/CLAUDE.md` |
| A procedure with several steps | A new or existing skill |
| An "always" or "never" that should happen without the agent choosing it | A hook or a permission rule in settings |
| Something a tool can check | A lint rule or a test |

The store is local, but most promotion targets are committed and shared. In the plan, flag every promotion that would publish a personal note to teammates. If it should stay personal, keep it in memory instead.

Present the plan as a table (memory, action, target, reason), with stale memories and shared promotions called out. Change nothing until the user approves: purging and rewriting destroy the only copy.

## 4. Apply

Apply what the user approved. For each promotion:

- Check the target for an existing rule on the same behaviour. Merge into it or reconcile with it rather than adding a second one.
- For a new skill, use the skill-creation workflow.
- Delete the memory file and its index line, so the fact lives in one place.

Done means every memory in the plan was applied or explicitly skipped, the index and directory match, and `MEMORY.md` is under 150 lines. Report the line count before and after, and the files changed outside the store. Don't commit.
