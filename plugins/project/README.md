# project

Sets up a repo so an agent can work in it easily.

## Install

Add the [agent-harness](../..) marketplace, then install the plugin:

```sh
claude plugin install project@agent-harness
```

## Skills

| Skill | Description |
|---|---|
| `/project:tune` | Set up or improve how Claude works in a repo, including how it verifies changes in the running app, or decide where one convention, permission, or tool belongs. Plans changes and applies them once you approve. |
| `/project:audit` | Check how well a repo is set up for Claude, read-only, in a separate context. `tune` runs it first. |
