# Analysis

Analysis is staged, isolate-only, and scoped by kind. The model does not own numbers.

## Stage contract

```text
convert → extract → validate → reflect → draft_report
```

Each stage reads the previous checkpoint. If the Worker dies, the next session resumes from `pipeline/jobs/<job-id>.md`.

### convert

- PDF/DOCX: `convert_document` → `conversion.md` in the job scratch, then into the archive shard.
- Image: vision/OCR tool if listed; otherwise mark `unreadable`.
- Audio: `transcribe_audio`.
- Reuse an existing conversion for the same asset hash.

### extract

Pull only what the page states:

- document type, facility, event date
- as-stated diagnoses, medications, follow-ups
- labs and vitals with value, unit, printed range, printed H/L flag
- quote + locator (page, heading, or nearby text)

Write rows into `evidence.md`. One row, one fact.

### validate

Reject a row that has a number without unit when the source had a unit. Keep `unknown` rather than filling from memory. Schema-check dates as ISO when possible.

### reflect

Required before merge:

- unit aliases (mmol/L vs mg/dL) without converting unless both sides are in the source
- duplicate dates
- conflicts with earlier **archive** evidence
- conflicts with Candidate background, quoted not resolved
- unreadable pages

Do not call this a diagnosis pass.

### draft_report

Kind scopes what may be drafted:

| Kind | Draft | Must not do |
|---|---|---|
| `daily_record` | daily trend table + optional sparkline | rewrite visit reports |
| `hospital_checkup` | visit report bound to this shard | declare disease |
| `diagnosis_report` | as-stated problem list + clinician questions | restated as our diagnosis |

## JS / isolate calculation

Compute in order, from evidence rows, not from prose:

1. Sort by event date.
2. Group by measurement name + unit.
3. Delta = current − previous when both exist and units match.
4. Flag only printed H/L or printed range breaches.
5. Emit a Markdown table, then optional Mermaid `xychart-beta` or `timeline`.

If a value cannot be parsed, skip it in the chart and list it under unknowns. The model may narrate the table; it may not add points.

## Invalidation

Every evidence row needs a stable id `ev-<shard-slug>-<n>`. Every draft stores `evidence_set` as those ids plus shard paths. Reports list dependents in their manifest (`rebuilds: visit | daily | diagnosis | questions`).

Bump `pipeline/queue.md` `generation` when:

- a new evidence row is committed
- an evidence shard is superseded
- kind is reclassified
- the Operator accepts a correction

A merge job records `observed_generation` at start. If generation differs at commit, discard and enqueue `reason: invalidation`.

Every draft stores the evidence set it used.

| Change | Rebuild |
|---|---|
| New daily record | `reports/daily/` and daily parts of `dash/` |
| New checkup | that visit report + `reports/trends/` lab dash |
| New diagnosis report | diagnosis packet + `dash/questions.md` |
| Evidence correction | every report that cited that row |
| Kind reclassified | both old and new scopes |

Never edit a published report file. Write `vN+1` and move the dash pointer only after review rules in `15-report-summary.md`.

If a job's observed archive generation no longer matches `pipeline/queue.md` generation, discard merge and enqueue reanalysis.
