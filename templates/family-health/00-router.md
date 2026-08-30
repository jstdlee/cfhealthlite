# Family Health router

This pack is what you load into CFAgent Lite: copy this directory to `/workspace/templates/family-health/`. This repository contains only that toolkit.

## Purpose

Use this pack to file one workspace Candidate's health materials, run a queued isolate pipeline, and keep shared archive, evidence, reports, and dash that any session agent can read. The pack organizes daily records, hospital check reports, and diagnosis reports. It does not diagnose, prescribe, or replace a clinician.

## Triggers

- The Operator drops files, photos, audio, email bodies, or notes into this workspace.
- The task asks for intake, archive, visit summary, daily trend, diagnosis-as-written packet, clinician questions, or pipeline status.
- A scheduled worker task should rebuild dash or trend reports from the shared archive.
- Candidate background, allergies, as-stated conditions, or follow-up dates need durable health context.

## Non-triggers

- Diagnosis, disease probability, medication start/stop, emergency triage, or treatment change.
- A household/family OS, cross-person ledger, or `person.json`.
- General medical research with no supplied records.
- Finance, legal, or insurance analysis beyond organizing the supplied record.

## Required inputs

- Materials in `/workspace/data/uploads` or `/workspace/data/health/inbox`, or text the Operator pasted.
- Desired output: archive only, visit report, daily trend, diagnosis packet, dash refresh, or clinician questions.
- Privacy constraint: keep materials inside this workspace unless the Operator asks to share.

Candidate identity is implied by the isolated workspace. Do not ask for a person id. Read `/workspace/data/health/background.md` before analysis when it exists.

## Phased workflow

Durable Family Health files live under `/workspace/data/health/`. That tree is the exception to the generic task-artifact report path in AGENTS.md and `templates/common/00-workspace-loop.md`. Task folders are scratch only.

Always load these before convert/OCR, even if they were not session-injected: `LAYOUT.md`, `11-strong-memory.md`, `12-intake.md`, `16-queue-pipeline.md`.

1. Load `11-strong-memory.md` and `/workspace/data/health/background.md` if present.
2. Load `LAYOUT.md` so outputs land in the shared health tree.
3. Load `10-target.md` to lock kind, date range, and deliverable.
4. Load `12-intake.md` and write the job onto `pipeline/queue.md` before converting.
5. Load `16-queue-pipeline.md` for stage checkpoints and generation.
6. Load `13-analysis.md` for extraction, JS summary, and invalidation.
7. Load `14-archive.md` to commit shards; never overwrite raw files.
8. Load `15-report-summary.md` for draft reports and dash pointers.
9. Load `17-cron-worker-tasks.md` only when scheduling rebuilds.
10. Use `20-progress-loop.md` across stages; use `30-quality-review.md` before delivery.

## Tool routing and fallback

Stay inside the Cloudflare isolate.

| Step | Use | Fallback |
|---|---|---|
| PDF / DOCX / HTML | `convert_document` to Markdown | Stop and mark unreadable; do not shell-parse |
| Photo / scan | listed vision / image description | Ask for a clearer photo; do not guess digits |
| Audio | `transcribe_audio` | Keep the asset, mark transcript missing |
| Numbers, deltas, flags | isolate JS / in-prompt calculation on extracted tables | Leave `unknown`; do not let the model invent a slope |
| Shape of trends / visits | Mermaid `timeline`, `xychart-beta`, `flowchart` | Markdown table for exact values |
| Recurring rebuild | CFAgent scheduled worker task | Manual dash refresh; do not fake a cron |
| Export | `share_file` / `share_directory` after Operator ask | Leave files in the workspace |

Do not use Python, PyMuPDF, Tesseract, local OCR, ntfy, or a second Health app. If a converter, transcription, or calculation tool is unavailable, process only readable text and record the blocker in the job shard.

## Checkpoints and recovery

Each Intake is one archive shard and one pipeline job. Stages are:

```text
queued → convert → extract → validate → reflect → draft_report → needs_review → current
```

Write a checkpoint to `pipeline/jobs/<job-id>.md` after every stage. Resume from the last successful stage. Retrying must reuse conversion and evidence files, not create a second archive shard.

A Worker isolate may die between stages. That is expected. Queue progress, not a single long agent run, is the source of pipeline truth.

## Verification gates

- Observations cite archive shard + quote or locator.
- Printed units, dates, medication names, and H/L flags stay as written.
- As-stated diagnoses stay quoted; the pack never adds a diagnosis.
- Unreadable / failed conversion is an event, never a report body.
- Checkup and diagnosis drafts stay `needs_review` until the Operator accepts them.
- Daily records update daily dash only; they do not rewrite hospital visit reports.
- A correction creates a new evidence/report version and invalidates dependents. It does not edit current files in place.

## Outputs and recap

Return links under `/workspace/data/health/` for archive shard, evidence, report, dash, and queue. Mention background updates separately. State job stage, blockers, and whether the dash pointer moved.

## Memory and experience sedimentation

Candidate background belongs in `/workspace/data/health/background.md`. Intake checklists, converter failures, and durable pack rules may be proposed for global Memory only when they are not private health facts. Do not store queue state, one-off job logs, or clinical details in Memory.
