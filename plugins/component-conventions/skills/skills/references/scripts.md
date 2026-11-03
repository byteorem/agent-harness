# Scripts

Prefer instructions over scripts. Reach for a script when the behavior must be deterministic, needs external tooling, or the agent would otherwise rebuild the same logic every run. Then give it a small library of helpers with the gotchas written into their docstrings, so every use carries them. The agent composes the helpers into one-off scripts for the question at hand, spending its turns on the analysis instead of the plumbing.

## Language and dependencies

Make each script self-contained, with its dependencies declared inline and pinned, so it needs no install step. For Python, that means a single file with PEP 723 metadata, run with `uv run --script`. When the skill wraps a tool or library from another ecosystem, use that ecosystem's language instead. For TypeScript, Bun can pin versions in the import path, but its auto-install turns off if a `node_modules` directory exists anywhere above the script.

## Referencing scripts

List every script in `SKILL.md` with a one-line purpose and the exact command to run it. Write paths relative to the skill's root (`scripts/validate.py`), in reference files too; the agent resolves them against the skill's directory.

## Designing for agents

Agents are the main user of a script, so design its interface for them:

- Never prompt. Take all input from flags, environment variables, or stdin. A missing input is an error that names the flag and shows usage, because a script waiting on input hangs the agent.
- Write errors the agent can act on: what went wrong, what was expected, and what to try.
- Reject ambiguous input instead of guessing, and accept closed sets of values where possible.
- Make reruns safe. Agents retry, so "create if missing" beats "fail on duplicate."
- Bound output size. Harnesses truncate long output, so default to a summary or a limit with a flag such as `--offset` for more, or require `--output` for large results.

## Examples

Helpers with the gotchas written into their docstrings:

```python
# scripts/signups.py
def fetch(day):
    """Signups from events.raw for one day.
    - event='signup_completed', NOT 'signup_started'
    - dedupe by anonymous_id; user_id is null until after signup"""

def by_referrer(df):
    """Group by traffic source.
    - '(direct)', '', and None all mean organic"""

def by_landing_page(df):
    """Group by entry page.
    - '/', '/index', '/home' are all the homepage
    - strips query params so UTM'd links collapse"""
```

The one-off script the agent composes from them for the question at hand:

```python
from signups import fetch, by_referrer, by_landing_page

mon, tue = fetch("2024-03-11"), fetch("2024-03-12")
print(by_referrer(tue) - by_referrer(mon))          # organic -60%, paid flat
print(by_landing_page(tue) - by_landing_page(mon))  # homepage specifically
# → something broke on / on Tuesday
```

A self-contained script with pinned, inline dependencies:

```python
# /// script
# requires-python = ">=3.12"
# dependencies = ["beautifulsoup4>=4.12,<5"]
# ///
```

Listing scripts in `SKILL.md`:

```markdown
## Scripts

- `uv run --script scripts/validate.py <file>`: validates a config file
- `uv run --script scripts/process.py --input <file>`: summarizes the results as JSON
```

A missing input that fails with guidance instead of prompting:

```text
$ uv run --script scripts/deploy.py
Error: --env is required. Options: development, staging, production.
Usage: deploy.py --env staging --tag v1.2.3
```
