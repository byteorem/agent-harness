# memory

Gives agents a local memory store per project in place of Claude's auto memory, and reviews it to promote what lasts into CLAUDE.md.

## Install

Add the [agent-harness](../..) marketplace, then install the plugin:

```sh
claude plugin install memory@agent-harness
```

Run `/memory:setup` once in each project. It creates the `.claude/memory/` store with a `MEMORY.md` index, loads the index every session through a gitignored `.claude/rules/memory.md`, keeps both out of git with `.git/info/exclude`, and turns off auto memory in `.claude/settings.local.json`.

## Skills

| Skill | Description |
|---|---|
| `/memory:setup` | Set up this project's local memory store and turn off Claude's auto memory. |
| `/memory:remember` | When and how agents save to and read from the store. Agents use it on their own; you can also call it directly. |
| `/memory:review` | Verify, merge, purge, or promote memories into more durable homes such as CLAUDE.md. |
