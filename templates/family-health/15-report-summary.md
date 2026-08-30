# Report and dash

Reports are versioned files in the shared tree. Dash files are current pointers any session agent can open.

## Report files

```text
/workspace/data/health/reports/visits/YYYYMMDDTHHMM-visit-vN.md
/workspace/data/health/reports/daily/YYYYMMDDTHHMM-daily-vN.md
/workspace/data/health/reports/diagnosis/YYYYMMDDTHHMM-as-stated-vN.md
/workspace/data/health/reports/trends/YYYYMMDDTHHMM-trend-vN.md
```

Each report starts with a manifest:

```markdown
# manifest
- kind:
- status: draft | needs_review | current | superseded
- evidence: [relative archive paths]
- job_id:
- previous:
```

## Sections

Use only what the kind allows.

Visit / checkup:

- What arrived
- Observations with citations
- Conflicts and duplicates
- Unknowns / unreadable
- Follow-ups **as written on the record**
- Mermaid timeline if chronology matters

Daily:

- Table of dated measurements
- JS deltas
- Optional `xychart-beta`
- Gaps in the log

Diagnosis-as-written:

- Quoted assessment text
- Quoted medications
- Quoted follow-up dates
- Clinician questions that point at missing or conflicting evidence
- No restated disease label from the model

## Review

Checkup and diagnosis reports stay `needs_review` until the Operator accepts, rejects, or corrects.

A correction is a Review note in the job file and a short review paragraph at the top of the next report version:

- target (report section or evidence row)
- action: approve | reject | correct | ask
- patch if correcting a parsed value

Operator accept → write the next version as `current`, record the previous report path in the new manifest, and point dash at the new file. Do not edit an older published report just to change its status.

Silent pipeline must not jump `needs_review` → `current`.

## Dash, shared by every session

Keep these short and current:

```text
/workspace/data/health/dash/overview.md
/workspace/data/health/dash/timeline.md
/workspace/data/health/dash/questions.md
```

`overview.md` links the current visit, daily, trend, and diagnosis reports. It does not duplicate full evidence.

Any new Family Health session reads dash first, then archive, then `/workspace/data/health/background.md` when present. It does not hunt through old task artifact directories.

## Presentation

- Exact numbers: Markdown tables
- Chronology: Mermaid `timeline`
- Small numeric shape: `xychart-beta`
- Pipeline / invalidation: `flowchart` or `stateDiagram-v2`

Follow `templates/common/10-mermaid-diagram-guide.md`.
