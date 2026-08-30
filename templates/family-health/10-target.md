# Family Health target

Lock these before conversion. Do not convert, OCR, or transcribe until `pipeline/queue.md` has this job at `queued` and `/workspace/data/health/background.md` has been read when present.

Durable outputs go to `/workspace/data/health/`, not to a task-folder report as the only copy.

- Kind: `daily_record`, `hospital_checkup`, or `diagnosis_report`. If mixed files arrived, split into separate Intake jobs.
- Event date vs intake date. Do not treat file mtime as the clinical date.
- Deliverable: archive only, visit report, daily trend, diagnosis-as-written packet, dash rebuild, or clinician questions.
- Date range for trend jobs.
- Whether this run may move `dash/` pointers or must stop at `needs_review`.

Default: checkup and diagnosis stop at `needs_review`. Daily extract may update `dash/timeline.md` as draft points but still records the job.

Read Candidate background from `/workspace/data/health/background.md` first. If background needed for interpretation is missing, ask one question or mark it unknown. Do not invent age, conditions, or medications.
