---
name: setup
description: Set up this project's local memory store and turn off Claude's auto memory.
disable-model-invocation: true
---

Set up a per-project memory store that stays on this machine, and turn off Claude's auto memory so the two don't compete. Safe to re-run: check each piece below, report the ones already in place, and do only what's missing.

## The layout

Work from the repo root (`git rev-parse --show-toplevel`):

| Path | Holds |
|---|---|
| `.claude/memory/MEMORY.md` | The index: one line per memory. Starts empty. |
| `.claude/memory/<type>_<slug>.md` | One memory each, written by the `memory:remember` skill. Setup creates none. |
| `.claude/rules/memory.md` | The loader: rules files load every session, and its import pulls in the index. |

Write the loader with exactly this content:

```markdown
# Agent memory

Notes agents saved in past sessions of this project. Each line below points to a memory file in `.claude/memory/`; read it when the line applies. Save, update, or forget memories with the `memory:remember` skill.

@../memory/MEMORY.md
```

## Steps

1. **Check the worktree.** If `git rev-parse --git-dir` differs from `git rev-parse --git-common-dir`, this is a linked worktree. A store here disappears when the worktree is removed, so tell the user and ask whether to set up in the main checkout instead.
2. **Create the index and loader** from the layout above.
3. **Keep them local.** Add `.claude/memory/` and `.claude/rules/memory.md` to the file at `git rev-parse --git-path info/exclude`. That keeps them out of git without editing a committed `.gitignore`. Confirm with `git check-ignore`.
4. **Turn off auto memory** by setting `"autoMemoryEnabled": false` in `.claude/settings.local.json`. Merge it into any existing JSON, and confirm the file is ignored too.
5. **Offer to import auto memories.** Claude's auto memory lives in `autoMemoryDirectory` if any settings file sets it. Otherwise it's under `~/.claude/projects/`, in the folder whose name is the repo path with each separator replaced by `-` (`/home/me/my-app` becomes `-home-me-my-app`). If that folder has a `memory/` with content, show the user its index and ask which memories to import. Auto memory uses the same layout, so copy each approved file into `.claude/memory/` and its line into the index. In the frontmatter, drop `originSessionId`, and set `updated` from `modified` or, failing that, from the file's modification date. Leave the originals where they are and tell the user the path.
6. **Report** what you created, changed, and found already in place. Tell the user to start a new session and check that `/context` lists `.claude/rules/memory.md` under Memory files.

## Gotchas

- Put the import in the rules file, not in `CLAUDE.local.md`. Creating a `CLAUDE.local.md` can change which of the project's instruction files Claude loads. A rules file leaves them alone.
- An `@` import resolves relative to the file it's written in, which is why the loader imports `../memory/MEMORY.md`.
