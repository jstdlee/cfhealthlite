# CFHealth Lite

Candidate health toolkit for [CFAgent Lite](https://github.com/jstdlee/cfagentlite).

**Load `templates/family-health/` into CFAgent Lite. That is the only folder the agent needs.**

This repo is not a web app. It is the Family Health pack, published on its own so you can edit it without forking the whole agent.

[CFAgent Lite](https://github.com/jstdlee/cfagentlite) · [Deploy CFAgent Lite](https://deploy.workers.cloudflare.com/?url=https://github.com/jstdlee/cfagentlite)

## What to load

```text
templates/family-health/     ← load this into cfagentlite
```

Copy destination inside a CFAgent Lite workspace:

```text
cfhealthlite/templates/family-health/
  →  /workspace/templates/family-health/
```

Then start a **new** session and enable toolkit **Family Health**. Do not use the old name **Health records**.

Latest cfagentlite already ships a copy of this pack. Copy from this repo only when you have edited the prompts and want to pin the update.

```bash
rsync -a --delete \
  templates/family-health/ \
  /path/to/workspace/templates/family-health/
```

Or in CFAgent Lite chat: upload the markdown files from `templates/family-health/` into `/workspace/templates/family-health/`.

## How the two repos fit

```text
cfagentlite                          cfhealthlite (this repo)
├── Worker + DO + R2 + Workers AI    └── templates/family-health/  load this
├── templates/family-health/
│   (shipped copy)
└── /workspace/data/health/          written by the agent, not by git
```

1. Deploy or run CFAgent Lite.
2. Bind the session toolkit **Family Health**.
3. Drop daily notes, 体检 PDFs, 化验 photos, 诊断 reports, audio, or pasted text into `/workspace/data/uploads` or `/workspace/data/health/inbox`.
4. The agent follows `templates/family-health/00-router.md`, writes `/workspace/data/health/`, and keeps checkup/diagnosis drafts at `needs_review` until you accept.

Start here: [`templates/family-health/00-router.md`](templates/family-health/00-router.md). File layout: [`LAYOUT.md`](templates/family-health/LAYOUT.md).

## What the agent writes

Durable health files stay in the CFAgent workspace, not in this git repo:

```text
/workspace/data/health/
├── background.md
├── inbox/
├── archive/YYYY/MM/YYYYMMDD-<kind>-<slug>/
├── reports/
├── dash/
└── pipeline/queue.md
```

Kinds: `daily_record`, `hospital_checkup`, `diagnosis_report`. Printed 诊断 is quoted as-stated. The pack does not diagnose, assign disease probability, or instruct medication changes.

## Sibling pack

Household money uses [cffinancelite](https://github.com/jstdlee/cffinancelite) the same way: load `templates/family-finance/` and enable **Family Finance**.
