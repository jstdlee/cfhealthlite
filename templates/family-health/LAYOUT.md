# Family Health shared layout

This workspace is one Candidate. Do not create `person.json`, member lists, or per-person databases. Candidate identity is the workspace itself. Stable background belongs in `/workspace/data/health/background.md`, not global `.agents/memory.md`.

The health tree is **shared**. Any session agent may read reports, dash, archive, and pipeline progress. Do not keep the only copy of a report under a task folder.

```text
/workspace/data/health/
├── inbox/                         newly dropped files waiting for a job
├── archive/                       immutable shards, never overwritten
│   └── YYYY/MM/YYYYMMDD-<kind>-<slug>/
│       ├── raw/                   original bytes
│       ├── conversion.md          convert_document / OCR / STT cache
│       ├── evidence.md            extracted facts with locators
│       └── meta.md                kind, source, dates, job id, hashes
├── reports/                       versioned narrative artifacts
│   ├── visits/
│   ├── daily/
│   ├── diagnosis/
│   └── trends/
├── dash/                          current pointers any session can open
│   ├── overview.md
│   ├── timeline.md
│   └── questions.md
└── pipeline/
    ├── queue.md                   inbox and job board
    └── jobs/<job-id>.md           stage checkpoints
```

## Shards

One archive shard is one Intake. Name it `YYYY/MM/YYYYMMDD-<kind>-<slug>/`:

```text
archive/2026/06/20260604-hospital_checkup-annual-physical/
archive/2026/08/20260830-daily_record-home-bp/
archive/2026/03/20260312-diagnosis_report-clinic-note/
```

If the event date is unknown, use the intake date and mark `event_date: unknown` in `meta.md`.

Kinds:

- `daily_record` — home logs, symptoms, device photos, voice notes
- `hospital_checkup` — 体检, 化验, imaging, hospital packets
- `diagnosis_report` — 诊断证明, discharge, clinic assessment as written

## Current vs history

- `archive/` and `reports/` are append-only shards.
- `dash/` holds the current readable summary. Regenerating dash does not delete old reports.
- A new report version is a new file. Name it `YYYYMMDDTHHMM-<topic>-vN.md`.
- Point dash files at the current report with a relative link. Do not rewrite the previous report.
- Review notes live in the job file and in the next report version, not in a separate `reviews/` tree.
- `pipeline/queue.md` `generation` is an integer. Bump it when evidence rows that current reports depend on change.

## Task folders

`/workspace/artifacts/tasks/<task-id>/` is scratch for the active job: conversions in progress, logs, failed attempts. Before a job completes, copy durable outputs into `/workspace/data/health/`. Later sessions must not need that task folder to understand the Candidate.

## Sharing

Reports and dash are workspace files. Use `share_file` / `share_directory` only when the Operator asks to export. Do not publish health files by default.
