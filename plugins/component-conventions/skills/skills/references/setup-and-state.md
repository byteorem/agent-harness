# Setup and state

Keep config and state in a stable data directory outside the skill's own directory. A skill's directory is often a git working tree or a plugin cache that updates replace, so anything written beside `SKILL.md` either lands in a commit or gets wiped.

## Setup

Some skills need context from the user before they can run, such as which channel to post to or which format to follow. Store those answers in a config file and have the skill check it first. If it is missing, the agent asks the user, using a structured multiple-choice question tool if it has one, saves the answers, and doesn't ask again.

```markdown
## Config

Read `<data-dir>/standup-post/config.json`. If it doesn't exist, ask the user:
- Which channel should standups go to?
- Paste a sample standup you liked.

Write the answers to that file. Otherwise, post to the saved channel using
the saved format.
```

## Memory

A skill can keep state between runs: an append-only log, a JSON file, or a SQLite database for anything bigger. A standup skill that logs every post can read its own history and see what changed since yesterday. Reading this log, the agent can see that the onboarding redesign has run for three days and is now blocked:

```text
2024-03-11
- shipped the cache invalidation fix
- started on the onboarding redesign

2024-03-12
- onboarding redesign: first two screens done
- reviewed Sarah's auth PR

2024-03-13
- onboarding redesign: blocked on copy review
- picked up the flaky-test ticket while waiting
```
