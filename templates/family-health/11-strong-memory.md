# Candidate background

This workspace is one Candidate. Do not create `people.json`, `person.json`, or a member roster.

Clinical background is private health context. Keep it in the health tree, not in global `.agents/memory.md`, because global Memory is loaded for unrelated sessions and tasks.

## Where it lives

Keep a single file at `/workspace/data/health/background.md`:

```markdown
# Candidate background

Stable Operator-stated or archive-approved facts. Not inferred diagnoses.

- Preferred name or how to address the Candidate:
- Age or birth year if supplied:
- As-stated conditions (quoted, with archive shard if from a record):
- As-stated medications and doses:
- Allergies:
- Devices used at home (BP cuff, glucometer):
- Units the records usually use:
- Hospitals / clinicians named in records:
- Follow-up dates the Operator cares about:
- Privacy notes:
```

## What may enter this section

Allowed:

- Facts the Operator stated as standing background.
- Facts copied from an **approved** archive evidence row, with shard path.
- Corrections that supersede an earlier background line.

Forbidden:

- Model-inferred diagnoses or probabilities.
- Queue status, job ids, or one-off OCR notes.
- Other people's records.
- Secrets, raw file contents, or full report dumps.

## How agents must use it

Every Family Health job loads this file before extract or report. Treat it as durable context, not as evidence. Reports still cite archive shards. `background.md` is for health-workflow continuity; evidence is for proof.

If a new checkup contradicts background (for example a medication list changed on paper):

1. Quote both.
2. Do not silently overwrite background.
3. Propose a `background.md` update and wait for Operator confirmation, unless the Operator already asked to save the correction.

After writing `background.md`, read it back before claiming the health background was updated.
