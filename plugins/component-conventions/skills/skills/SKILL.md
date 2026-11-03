---
name: skills
description: Conventions for writing agent skills. Use alongside the agent's built-in skill-creation skill or workflow whenever creating, editing, or reviewing a skill (a SKILL.md and its directory). Adds conventions; does not replace that workflow.
---

Load these conventions alongside the agent's own skill-creation workflow, not instead of it. That workflow covers the process and the format; these add opinionated conventions, and where the two conflict, these win. If two of these conventions pull against each other for the task at hand, tell the user rather than quietly picking one.

## Every skill

- **Scope it to one job.** Scope a skill like a function: one coherent unit of work that composes well with other skills. Too narrow, and a single task needs several skills loaded at once, adding overhead and conflicting instructions. Too broad, and the description can't say precisely when to use it. Querying a database and formatting the results is one job; adding database administration makes it two.
- **Don't state the obvious.** The agent already knows how to code and can read the codebase. A skill that restates what it would do by default costs context and adds nothing. The best skills encode opinions, knowledge, and practices particular to the user, their team, or their product: team taste, local decisions, and knowledge the agent could not infer. Test each instruction by asking whether the agent would get it wrong without it; if not, cut it.
- **Keep the root clean.** `SKILL.md` is the only file at the skill's root. Everything else goes in a subdirectory: `scripts/`, `references/`, `assets/`, or another named for what it holds.

## When to read more

Read each file whose row applies to the skill at hand. When reviewing a skill, that is usually most of them.

| When | Read |
|---|---|
| Writing or reviewing a description | `references/descriptions.md` |
| Deciding whether the agent or only the user can invoke the skill | `references/invocation.md` |
| Writing or reviewing the skill's instructions | `references/calibrate-control.md` |
| The agent tripped on something, or the skill has no gotchas yet | `references/gotchas.md` |
| The skill has output with a required shape, a multi-step workflow, or batch or destructive operations | `references/instruction-patterns.md` |
| The skill is long, has several workflows, or carries material only some runs need | `references/progressive-disclosure.md` |
| The skill needs config from the user or keeps state between runs | `references/setup-and-state.md` |
| The skill runs commands or ships scripts | `references/scripts.md` |
