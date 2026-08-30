# Family Health quality review

Before delivery:

- Kind is correct (`daily_record` / `hospital_checkup` / `diagnosis_report`).
- Archive shard exists with raw + conversion or `unreadable.md`.
- Every observation cites a shard quote or locator.
- Numbers, units, dates, and H/L flags match the source or are marked unknown.
- JS deltas were computed from evidence rows, not from model prose.
- No diagnosis, disease probability, or medication-change instruction.
- Printed 诊断 is quoted as-stated, not adopted as pack output.
- Checkup/diagnosis report is `needs_review` unless the Operator accepted it.
- Daily work did not rewrite a visit report.
- Dash links resolve under `/workspace/data/health/` and are usable from a fresh session.
- Queue board matches actual job stage.
- Candidate background changes were proposed or verified in `/workspace/data/health/background.md`, not stored as `person.json` or global Memory.
- Diagrams follow the common Mermaid guide; tables hold exact values.

Lead with archive/report/dash links, job stage, and remaining blockers.
