# Instruction patterns

Use these where they fit; most skills need one or two.

- **Output templates.** When output has a required shape, give a template instead of describing it, since agents match concrete structure better than prose. Short templates go inline; long or situational ones go in `assets/`.
- **Checklists.** For multi-step workflows with dependencies or gates, a checklist keeps the agent from skipping steps.
- **Validation loops.** Do the work, run a validator (a script, a reference checklist, or a self-check), fix what it finds, and repeat until it passes.
- **Plan, validate, execute.** For batch or destructive operations, have the agent write its plan to a structured file, check that file against the source of truth with a script, and only then execute. Make the validator's errors specific enough to self-correct from.

## Examples

Output template:

```markdown
## Report structure

Use this template, adapting sections to the analysis:

# [Analysis title]

## Summary
[One paragraph of key findings]

## Findings
- Finding, with supporting data

## Recommendations
1. Specific, actionable recommendation
```

Checklist:

```markdown
Progress:
- [ ] Analyze the form: `scripts/analyze_form.py`
- [ ] Map fields: edit `fields.json`
- [ ] Validate the mapping: `scripts/validate_fields.py`
- [ ] Fill the form: `scripts/fill_form.py`
- [ ] Verify the output: `scripts/verify_output.py`
```

Validation loop:

```markdown
1. Make the edits.
2. Run `scripts/validate.py output/`.
3. If it fails, read the error, fix the issue, and run it again.
4. Continue only when validation passes.
```

Plan, validate, execute:

```markdown
1. Extract the form's fields: `scripts/analyze_form.py input.pdf` → `form_fields.json`
2. Write `field_values.json`, mapping each field name to its value.
3. Validate: `scripts/validate_fields.py form_fields.json field_values.json`
4. If validation fails, revise `field_values.json` and validate again.
5. Fill the form: `scripts/fill_form.py input.pdf field_values.json output.pdf`
```

A validator error the agent can self-correct from:

```text
Field 'signature_date' not found. Available fields: customer_name, order_total, signature_date_signed
```
