---
name: remember
description: Use when something should outlast this session and the code won't show it, such as when the user corrects you or confirms an approach, states a preference or their role, explains a decision, or points you to an outside resource. Also use when the user asks you to remember or forget something.
---

The project's memory lives in `.claude/memory/` at the repo root. `MEMORY.md` is the index and is already loaded into your context. Each memory is its own file in the same directory, which you read when its index line applies. If `MEMORY.md` doesn't exist, the store hasn't been set up. Tell the user to run `/memory:setup` rather than creating it yourself, because setup also keeps the store out of git and turns off Claude's auto memory.

## What to save

Save what a future session would get wrong without it, sorted by type:

| Type | Holds |
|---|---|
| `user` | The user's role, expertise, and working preferences |
| `feedback` | Corrections the user gives you, and approaches they confirm when the choice wasn't obvious |
| `project` | Decisions, constraints, and context the code and git history don't explain |
| `reference` | Where to find things outside the repo, such as an issue tracker, dashboard, or doc |

Some things belong somewhere else:

| If it's | Put it |
|---|---|
| Work in progress or next steps | The task tracker, not memory |
| Already in the project's instruction files or rules | Nowhere; it's covered |
| Readable from the code, config, or `git log` | Nowhere; look it up next time |
| An automatic behaviour ("every time X, do Y") | A hook. Tell the user, since memory can't make anything happen |
| A secret, token, or credential | Nowhere |

## Writing

1. **Check the index first.** If a memory already covers this, update that file instead of adding a second one. If the new fact contradicts it, rewrite it to the current truth.
2. **Write the memory file** as `<type>_<slug>.md`:

   ```markdown
   ---
   name: Use pnpm, not npm
   description: The repo is pnpm-only; npm rewrites the lockfile
   type: feedback
   updated: 2026-11-02
   ---
   Install and run scripts with pnpm. Never run npm in this repo.

   **Why:** npm rewrites `pnpm-lock.yaml` and CI rejects the diff. The user corrected this after an `npm install`.

   **How to apply:** Use `pnpm` for every install, add, and script run, including in suggested commands.
   ```

   - `description` is one line that lets a future session judge relevance without opening the file.
   - Give `feedback` and `project` memories the **Why:** and **How to apply:** lines. The reason is what lets a future session handle a case the rule didn't anticipate.
   - Write for an agent that never saw this session: name files and commands in full, and use absolute dates, not "yesterday" or "last week".
   - Set `updated` to today's date each time you write the file.
3. **Update the index.** Add or edit one line, `- [<name>](<file>) — <hook>`, keeping the hook under about 150 characters. The index holds pointers only; put the content in the memory file.
4. **Tell the user in one line** what you saved, so they can veto it.
5. **Check the size.** If `MEMORY.md` is now over 150 lines, tell the user to run `/memory:review`.

To forget something, delete its file and its index line.

## Reading

Read a memory file when its index line bears on the work in front of you. A memory records what was true on its `updated` date. Before acting on one that names a file, function, or command, check that it still exists. If the memory is wrong, fix or delete it in the same session.
