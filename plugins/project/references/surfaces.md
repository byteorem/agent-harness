# Surfaces

Formats and paths change between releases, so fetch a surface's doc page before changing it. Index: https://code.claude.com/docs/llms.txt.

**Scope.** Use the narrowest scope that fits, and user scope only when the user chooses it. Committing publishes a file, so moving anything personal to project scope needs the user's say-so.

**Owners.** If a plugin, a skill, or the surface's own files say that something else manages a surface, leave its contents to that owner. Report only where it conflicts with the rest of the setup.

| Surface | What belongs |
|---|---|
| Instructions | Conventions and their reasons: what Claude would get wrong and can't find by looking |
| Memory | Learned notes, such as corrections, preferences, and hard-won facts. They're provisional and often personal; durable ones move to instructions |
| Tasks | Work state that outlives a session |
| Skills | Multi-step procedures, and reference that only some tasks need |
| Subagents | Work that reads a lot and returns little, or needs fewer tools |
| Hooks | Anything that must always or never happen |
| Permissions | Allow rules for safe, repeated commands, especially verification. Deny rules for dangerous ones |
| Settings | Env, model, sandbox |
| MCP servers | Project scope only if the whole team uses the server and it holds no personal credentials |
| Plugins | Project scope when only this repo needs the plugin |

Rules:

- An instruction is a request. A rule that must hold every time goes in a hook or permission rule.
- The docs also call instruction files "memory". Here, memory means only the learned notes. Defer to whichever memory mechanism the project uses. Tuning writes no memory entries.
- One job per skill. A skill with side effects runs only when the user invokes it.
