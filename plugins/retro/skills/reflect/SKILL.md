---
name: reflect
description: Run a retrospective on a session and propose changes to the agent's setup so future sessions go better.
disable-model-invocation: true
argument-hint: "[session, default the current one]"
---

Run a retrospective on a session. Find where the agent's environment (its instructions, skills, checks, tools, and access) made the work slower, costlier, or wrong, and propose changes to that environment rather than to the work itself. Session: $ARGUMENTS (if empty, the current one). Change nothing until the user has picked what to apply.

## 1. Get the record

Work from your context for the current session, falling back to its transcript (session `${CLAUDE_SESSION_ID}`) for turns lost to compaction. For other sessions, including their subagents, read the transcripts. They run long, so have a subagent search them and return a timeline of the moments in step 2, each with a short quote.

Read the tool calls and their results, not just the conversation. Much of the friction never reaches the chat.

## 2. Find the moments

- The user corrected or redirected the agent
- A mistake surfaced late (from the user, review, or CI), or never got caught
- A long search for a file or a fact
- A failing command run again, or a permission prompt or denial
- A tool call that returned far more than the agent used
- Something the agent needed and couldn't reach: logs, a service, a doc
- A skill that should have fired and didn't, or fired and misled

## 3. Diagnose

Before proposing anything, read the setup that was in force: the instruction files and their imports, `.claude/rules/`, skills, hooks, settings, and the repo's lint, test, and CI config. A check that exists but isn't wired up, or a rule that exists but was ignored, is the finding. Don't propose a second one.

Match each moment to a category. Skip any category no moment points to.

| Category | Look for | Usual fix |
|---|---|---|
| Navigation | Time spent finding things; hidden coupling between files | A pointer in the instructions, or a reference doc that a skill points to |
| Checks | A mistake a tool could have caught; no pre-commit hook or CI job running lint, typecheck, or tests | A lint rule, test, pre-commit hook, or CI job |
| Review | A judgement-call mistake that review missed | A rule for the project's reviewer or review checklist |
| Instructions | A rule ignored or with no effect; instruction files large enough to crowd the context | Move it to a check or hook, or cut it |
| Skills | A procedure done by hand; a skill that didn't fire or misled | A new skill, a description fix, or a gotcha |
| Tool economy | Expensive or repeated calls; prompts for safe commands | A narrower tool or flags, an allow rule, or a subagent |
| Information access | Information the agent lacked | Teed logs, or read-only MCP or CLI access |
| Memory | A correction worth keeping that fits none of the above | `memory:remember` |

- **A mechanical mistake gets a check.** A fixed pattern, banned API or command, import shape, or file-location rule belongs in a lint rule, hook, or CI job, whichever the repo makes cheapest. Default to building the check over writing the rule, and delete any instruction the check replaces. Keep prose rules for judgement calls.
- **Standards go to review.** The agent doing the work carries the most context, while a reviewer sees only the diff and has room for rules. Put standards where the diff gets reviewed, not in instructions every session loads. If the repo has no review step, that is a finding.
- **Instructions load every session.** Keep them for pointers and for what the agent can't find by looking. Anything that must hold every time belongs in a hook or permission rule.
- **Use the narrowest scope that fits.** Prefer the project. User scope only when the user chooses it.

## 4. Interview

Show the candidates as a ranked list, highest first, ranked by what the moment cost times how likely it is to recur:

```markdown
1. **<category>** · <moment, quoted or cited> · <what in the environment allowed it>. Fix: <change> → <surface>, <scope>.
```

Then ask the user about each candidate with `AskUserQuestion`, in rank order and as many per call as the tool allows. The dialog may hide the list, so name the candidate and its evidence in the question itself. Options: your fix first, marked as recommended; a real alternative if one exists, such as another surface or scope; and skip. In the question, flag any fix that writes to user scope, publishes something personal to a committed file, or deletes content.

Settle what the code or a sensible default already answers, and don't ask it. When an answer raises a new decision, such as where the chosen fix should live, ask it in the next round. If `AskUserQuestion` isn't available, ask in chat as a numbered list.

When nothing is left open, summarize what will be applied and what was skipped.

## 5. Apply

Apply what the user picked. Merge into an existing rule on the same behaviour instead of adding a second one. Route changes to an owned surface through its owner. Report the files changed and how to confirm each change took effect. Don't commit.
