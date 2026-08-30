# Cron via worker tasks

Use CFAgent scheduled jobs, not a Linux crontab and not an external notifier.

## When to schedule

Only if the Operator asks for a recurring rebuild, or a record itself contains a follow-up date they want remembered.

Allowed schedules:

| Job | Cadence | Reads | Writes |
|---|---|---|---|
| Drain inbox one stage | `5m` or `1h` if they want silent processing | `pipeline/queue.md` | next stage checkpoint |
| Rebuild daily/trend dash | `1d` or monthly | archive evidence | new trend report + dash pointer if policy allows |
| Follow-up reminder | dated `at` | dash + background follow-up dates | `dash/questions.md` draft |

Confirm script path, runtime, and next run before creating the schedule. Do not invent a cadence.

## What a scheduled run may do

Autonomous internal loop only:

- read shared archive and current dash
- run JS summary on already extracted evidence
- write a new versioned report with `status: needs_review` unless the Operator pre-approved dash refresh for daily trends
- append pipeline audit lines

A scheduled run may not:

- fetch new external medical sites
- send ntfy / email / chat outside the workspace
- move checkup/diagnosis dash pointers to `current`
- diagnose, score disease probability, or change medications
- create global Memory entries or clinical background updates without the usual confirmation and read-back gates

## Script shape

Keep the scheduled script under `/workspace/artifacts/tasks/scheduled/` unless the Operator gives another path. The script should be a short worker-shell entry that tells the agent:

1. Load this Family Health pack router.
2. Drain one queued job, or rebuild trend dash from archive.
3. Stop after one stage or one report.
4. Leave links in `pipeline/queue.md`.

Runtime: `worker-shell` only. Do not schedule Family Health drain or rebuild on `sandbox`. Conversion stays on listed Worker tools (`convert_document`, vision, `transcribe_audio`).

## Duplicate deliveries

One schedule occurrence = one job id derived from `schedule-id + next_run_at`. If the platform delivers twice, reuse the job file and do not write a second trend report for the same occurrence.
