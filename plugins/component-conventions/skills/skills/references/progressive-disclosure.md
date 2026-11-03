# Progressive disclosure

All of `SKILL.md` loads on every run, so it holds only what every run needs; the spec's ceiling is 500 lines and about 5,000 tokens. When a skill is long, has several workflows, or carries material only some runs need, split it out as far as it will go: many small files, each read only when it applies, with `SKILL.md` as a minimal router that gives the agent enough to know where to look.

Split by when the material is needed, and keep each topic's rules and examples in the same file. Tell the agent when to read each file: "Read `references/api-errors.md` if the API returns a non-200 status" beats "see `references/` for details." A file `SKILL.md` doesn't mention never gets read.

When each file covers one scenario, such as a symptom to investigate, fill it with what the agent can't infer: the exact commands, queries, or dashboards to check, what a bad result looks like, and what each result means. Leave out generic debugging steps; the agent already knows them.

## Example

A router `SKILL.md` for a skill with several workflows, about 30 lines in total:

```markdown
---
name: queue-debugging
description: Debug stuck, slow, or failing queue workers.
---

Match the symptom below and read the linked file for investigation steps.

| Symptom                      | Read                         |
|------------------------------|------------------------------|
| Jobs sit pending, never run  | references/stuck-jobs.md     |
| Messages in DLQ, no retries  | references/dead-letters.md   |
| Same job retried in a loop   | references/retry-storms.md   |
| Queue depth keeps climbing   | references/consumer-lag.md   |
```

And one of the files it routes to, `references/stuck-jobs.md`:

```markdown
# Stuck jobs

1. Check that the workers are alive: `kubectl get pods -l app=queue-worker`.
   Fewer than 3 Running means a crash loop; read `kubectl logs -l app=queue-worker --previous`.
2. Check for stale locks:
   `SELECT id, locked_by, locked_at FROM jobs WHERE status = 'pending' AND locked_at < now() - interval '10 min';`
   Any rows mean a worker died mid-job without releasing its lock. Clear them with `bin/release-stale-locks`, not by hand.
3. Check that the queue has subscribers: `bin/queue-config | grep <queue>`.
   If the queue is missing, it was added in code but not in `config/queues.yml`.

Report the cause, the output from the step that found it, and the action taken.
```
