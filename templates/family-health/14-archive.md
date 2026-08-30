# Archive

Archive is the shared source of truth. Chat, task folders, and Memory are not.

## Commit a shard

Commit as soon as `raw/` is stored. Do not wait for a successful extract.

Path:

```text
/workspace/data/health/archive/YYYY/MM/YYYYMMDD-<kind>-<slug>/
```

Required files:

- `raw/` original bytes, unchanged
- `meta.md` kind, hashes, job id, event date, intake date, source name, stage

Then add exactly one of:

- `conversion.md` plus `evidence.md` after a successful extract
- `unreadable.md` after convert/OCR/STT failure (no `evidence.md`)

Unreadable shards stay on the queue as `failed` until the Operator replaces the source. They must still exist so later sessions see the attempt.

`meta.md` fields:

```markdown
# meta
- kind:
- event_date:
- intake_date:
- source_name:
- channel:
- asset_hash:
- conversion_tool:
- job_id:
- stage:
- supersedes:          # shard path if this replaces a bad conversion
```

## Immutability

- Do not overwrite `raw/`.
- A better OCR of the same asset is a new shard with `meta.md` `supersedes` pointing at the old shard. Do not add `evidence-v2.md` beside the old file.
- Historical reports keep linking to the evidence version they used.

## Keywords

Optional tags in `meta.md`: `体检 化验 处方 病历 血压 血脂 血糖 肝功 肾功 甲状腺 用药 复诊`. Tags are indexes, not diagnoses.

## What archive is not

Do not recreate `data/organized/` from the old Gradio app. Do not shard by family member. The workspace already isolates the Candidate.
