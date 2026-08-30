# Family Health progress loop

1. Name kind, source, and deliverable in the job file. Enqueue before convert.
2. Advance one pipeline stage per isolate run when conversion is heavy.
3. After each stage, update `pipeline/jobs/<job-id>.md` and `pipeline/queue.md`.
4. Copy durable files into `/workspace/data/health/` before calling the stage done. A task-folder report is never the canonical copy.
5. Keep `/workspace/artifacts/tasks/<task-id>/` scratch disposable.
6. If interrupted, the next session reads queue + job file + dash, not chat history.
7. Re-read the newest Operator message before moving a dash pointer.
