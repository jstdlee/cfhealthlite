# Intake

Create one job per dropped item or per Operator-declared packet. Do not summarize until the Intake row exists.

## Channels

Accept only what this workspace can store:

- file (PDF, DOCX, image)
- photo / camera upload
- audio
- pasted text
- email body already saved into the workspace

Copy new files from `/workspace/data/uploads` into `/workspace/data/health/inbox/` if they are not already there. Leave originals in uploads unless the Operator explicitly asks to tidy them.

## Classify

Set `kind` from content, not filename alone:

| Signals | Kind |
|---|---|
| Home log, symptom diary, device photo, “today BP” | `daily_record` |
| 体检, 化验, hospital letterhead, lab table, imaging | `hospital_checkup` |
| 诊断证明, 出院, clinic assessment, ICD/diagnosis block as printed | `diagnosis_report` |

If kind is uncertain, keep `kind: unknown`, run convert/extract, and ask before writing a visit or diagnosis report.

## Intake record

Write `pipeline/jobs/<job-id>.md` with:

- job id, created at, source path
- kind, event date or `unknown`
- channel, media type
- stage = `queued`
- idempotency key = hash of source bytes plus kind

Append a line to `pipeline/queue.md`. Do not start analysis in the same breath if convert/OCR will be slow; enqueue first so another session can see progress.

## Safety at the door

- Do not OCR digits you cannot see.
- Do not treat a 诊断 printed on paper as this pack's diagnosis.
- Duplicate bytes: keep one raw asset, still record this Intake (new drop, same asset hash).
