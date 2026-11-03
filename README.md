# agent-harness

> Claude Code plugins that set up and maintain the harness an agent works in.

## Plugins

| Plugin | Description | Skills |
|---|---|---|
| [`component-conventions`](plugins/component-conventions) | Conventions for writing agent components such as skills, layered on top of the agent's built-in authoring workflow. | `skills` |
| [`project`](plugins/project) | Sets up a repo so an agent can work in it easily. | `tune`, `audit` |
| [`retro`](plugins/retro) | Reflects on completed work so the agent and your setup improve over time. | `reflect` |
| [`memory`](plugins/memory) | Gives agents a local memory store per project in place of Claude's auto memory, and reviews it to promote what lasts into CLAUDE.md. | `setup`, `remember`, `review` |

## Install

Add the marketplace, then install a plugin from it:

```sh
claude plugin marketplace add byteorem/agent-harness
claude plugin install <plugin>@agent-harness
```

This installs the plugin for every project. Add `--scope project` to install it for the current project only.

Skills run as `/<plugin>:<skill>`, such as `/project:tune`.

## Design principles

- **Narrowest scope that fits.** Project-specific tools stay in the project. Cross-project tools can be global if the user chooses that. Plugins don't go global on their own.
