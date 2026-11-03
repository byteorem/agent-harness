---
name: tune
description: Use when setting up or improving how Claude works in a repo, or deciding where a convention, procedure, guardrail, or tool belongs and at what scope. Also use when Claude keeps getting something wrong in a repo, or has no reliable way to verify changes in the running app.
---

Read `${CLAUDE_PLUGIN_ROOT}/references/surfaces.md`. If the work touches how the app runs or how changes get checked, also read `${CLAUDE_PLUGIN_ROOT}/references/verification.md`.

**One item** (a convention, a command, an "always" behaviour): pick its surface and scope, and propose the change in one line. Skip the audit.

**A whole repo:** run the `project:audit` skill, passing along the user's focus if they gave one. Turn the findings the user cares about into a plan with these columns: change, surface, scope, reason. Flag any change that commits personal config, writes to user scope, or deletes content.

**Uncommitted:** if the user wants none of the changes committed, put each change in its surface's local scope where it has one, such as `.claude/settings.local.json` for permissions, hooks, env, and plugins. A surface with no local scope keeps its usual project path. Add any new path that `git status` shows to the repo's exclude file (`git rev-parse --git-path info/exclude`; `.gitignore` is itself tracked, and in a worktree `.git` is a file), choosing the topmost path you created: the whole `.claude/` if the repo had none, otherwise the new subdirectory or file inside it. Excluding can't hide an edit to a tracked file, so mark those rows in the plan as needing a commit. Beside a tracked `AGENTS.md`, instructions have no safe local scope: a `CLAUDE.md` or `CLAUDE.local.md` makes Claude stop reading `AGENTS.md`, so mark those rows as needing a commit too.

Change nothing until the user approves the plan. When applying it, merge into an existing rule on the same behaviour instead of adding a second rule. Route changes to an owned surface through its owner.

Done means every approved change is applied or skipped, and, when uncommitted, `git status` lists nothing you created. Report the files changed and how to confirm that each change took effect. Don't commit.
