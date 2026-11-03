# Calibrate control

Think of a skill as a lightweight guide that helps the agent find what it needs, not a procedure it must follow. Agents follow instructions closely, and current models handle nuance well, so overly specific guidance now tends to hurt more than help; constrain tightly only where it matters most. Match how prescriptive each part is to how fragile it is, and calibrate each part on its own:

- Where several approaches work, state the goal and the constraints, and explain why. An agent that knows the purpose adapts better than one following a recipe.
- Where an operation is fragile, consistency matters, or the order is fixed, be exact: give the command and say not to change it.
- Teach how to approach a class of problems, not the answer for one instance. Specific formats, constraints, and tool names are fine; the method should generalize.
- When several tools would work, name one default and mention alternatives only as an escape hatch, not as a menu.
- When the skill does give steps, write them as imperatives with explicit inputs and outputs.

## Examples

Too prescriptive for a task that tolerates variation:

```text
Step 1: Run git log to find the commit.
Step 2: Run git cherry-pick <hash>.
Step 3: If there are conflicts, run git status to list them.
Step 4: Open each conflicting file.
Step 5: For each <<< marker, decide which side to keep.
Step 6: Run git add on each resolved file, then…
```

Better:

```text
Cherry-pick the commit onto a clean branch. Resolve conflicts preserving
intent. If it can't land cleanly, explain why.
```

Exact, because the operation is fragile:

````markdown
## Database migration

Run exactly this command. Do not modify it or add flags.

```bash
python scripts/migrate.py --verify --backup
```
````

An answer for one instance, then a method that works for any query:

```markdown
<!-- Only useful for this exact task -->
Join `orders` to `customers` on `customer_id`, filter `region = 'EMEA'`,
and sum `amount`.

<!-- Reusable -->
1. Read the schema from `references/schema.yaml` to find the relevant tables.
2. Join tables on the `_id` foreign key convention.
3. Turn the user's filters into WHERE clauses.
4. Aggregate as needed and format the result as a markdown table.
```

A menu, then a default with an escape hatch:

```markdown
<!-- Menu -->
You can use pypdf, pdfplumber, PyMuPDF, or pdf2image...

<!-- Default -->
Use pdfplumber for text extraction. For scanned PDFs, use pdf2image with
pytesseract instead.
```
