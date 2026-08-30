# Queue and pipeline progress

CFAgent chat is not the pipeline. Long convert/OCR/report work is a job board that survives the session.

## Board

Maintain `/workspace/data/health/pipeline/queue.md` as the shared board:

```markdown
# Health pipeline
generation: 12

## queued
- job-... kind=hospital_checkup source=... stage=queued

## running
- job-... stage=convert

## needs_review
- job-... report=...

## failed
- job-... unreadable blurry lab photo

## current
- dash pointers last moved at ...
```

Bump `generation` when archive evidence that current reports depend on changes. In-flight merge jobs that observed an older generation must requeue instead of committing.

## Job file

`/workspace/data/health/pipeline/jobs/<job-id>.md` is the checkpoint:

```markdown
# job-...
- kind:
- source:
- stage:
- attempt:
- idempotency:
- observed_generation:
- archive_shard:
- last_error:
- next:
```

Stages, in order:

```text
queued
convert
extract
validate
reflect
draft_report
needs_review
current
failed
```

Allowed recovery: `failed → queued`. Not allowed: `queued → current`, `needs_review → current` without Operator accept.

## How this maps onto CFAgent worker tasks

One pipeline job should become one CFAgent `agent_task` when the Operator is not sitting in chat:

- Task goal names the stage and the inbox path.
- Task run is one isolate attempt (convert this PDF, extract this conversion, draft this report).
- Task `blocked` maps to `needs_review` or missing tool.
- Task `completed` means the **stage** finished, not that dash is current.

If Cloudflare Queue is not bound, drain `pipeline/queue.md` with a CFAgent scheduled worker-shell task that picks the oldest `queued` job and advances one stage. Do not process the whole inbox in one isolate invocation. Do not use sandbox/Linux, Python, or Durable Object APIs the agent cannot bind.

## Parallel vs serial

- convert / extract: one file per run, may overlap across jobs
- reflect / draft / dash pointer: one active merge at a time for this workspace

Daily and checkup jobs may run convert in parallel. They must not write dash pointers in the same run unless scopes are disjoint (`daily` vs `visits`).

## Operator view

When asked “where is my report?”, read `queue.md` and answer with counts and links. Do not require the Operator to keep the chat open. Failed conversion stays on the board as `blocked` / unreadable.

## Reanalysis

Reviews, replacements, and new files enqueue a new job with `reason: invalidation` and the parent job id. The new job reads current archive, not the old draft prose.
