# Descriptions

At session start, the agent sees a list of each skill's name and description and decides from that alone whether a skill applies. The description is not a summary for humans. It is a trigger condition:

- Phrase it as an instruction to the agent ("Use when…"). Say what the skill does only as far as it helps decide when to use it.
- Describe the user's intent in the words a request would contain, not the skill's internals. Include cases where the user doesn't name the domain.
- Keep it as short as it can be while still making clear when to use it. Every description loads in every session, and the spec caps it at 1,024 characters.

## Examples

Weak, because it describes the skill to a human:

```yaml
name: babysit-pr
description: A comprehensive tool for monitoring pull request status across the development lifecycle.
```

Strong, because it says what the skill does and what triggers it:

```yaml
name: babysit-pr
description: Monitors a PR until it merges. Trigger on 'babysit', 'watch CI', 'make sure this lands'.
```

Too narrow, then fixed to cover requests that never name the domain:

```yaml
# Before
description: Process CSV files.

# After
description: Use when the user has a CSV, TSV, or Excel file and wants to explore, transform, or chart the data, even if they don't say "CSV" or "analysis."
```
