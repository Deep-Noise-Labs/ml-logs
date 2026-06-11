# Deep Noise Labs ML Logs

Central archive for Deep Noise Labs machine-learning training telemetry, observer reports, and raw solver logs.

This repository is intentionally lightweight: it stores human-readable logs and daily summaries rather than code. It is useful for reviewing model-training history, comparing experiment behavior across days, and keeping operational observations in version control.

## What is in this repository?

```text
ml-logs/
├── 0p7p2/
│   ├── solver.log.0
│   └── solver.log.1
├── 0p8p0A/
│   └── solver.log.0
├── 0p8p1/
│   └── solver.log.0
└── reports/
    └── clearml/
        ├── 2026-05-20.md
        ├── 2026-05-21.md
        └── ...
```

### Raw solver logs

The versioned experiment folders contain raw solver output from model-training runs. These logs preserve low-level training traces such as:

- distributed initialization details;
- AudioCraft / MusicGen solver startup information;
- dataset split paths loaded by the run;
- model size, checkpoint, and hash information;
- epoch-level train and validation summaries;
- checkpoint-save events;
- loss, cross-entropy, perplexity, gradient norm, duration, and related solver metrics.

Current raw log folders include:

| Folder | Contents | Typical use |
|---|---|---|
| `0p7p2/` | `solver.log.0`, `solver.log.1` | Historical solver logs for a MusicGen experiment line. |
| `0p8p0A/` | `solver.log.0` | Solver log for a later 0.8-series experiment. |
| `0p8p1/` | `solver.log.0` | Solver log for a later 0.8-series experiment. |

### ClearML daily reports

`reports/clearml/YYYY-MM-DD.md` contains daily observer reports generated from ClearML telemetry. These reports summarize active training jobs and provide a dated operational record.

Each report generally includes:

- executive summary;
- active task inventory;
- latest/min/max training and validation metrics;
- GPU, memory, disk, and runtime notes when available;
- operational concerns;
- recommended next actions;
- data-availability caveats.

Example topics covered in the reports include MusicGen CE/PPL trends, Foundation-1 diffusion loss behavior, stale telemetry checks, GPU utilization, validation-regression warnings, and missing metric caveats.

## Why this repository exists

Training runs can be long, expensive, and difficult to reconstruct after the fact. This repository provides a stable audit trail for:

1. **Experiment observability** — track what was active on each day and whether metrics looked healthy.
2. **Regression investigation** — compare historical loss/perplexity/resource patterns with current runs.
3. **Model-development memory** — preserve useful context from ClearML before dashboards or task views change.
4. **Operations review** — retain notes about GPU utilization, disk pressure, checkpoint cadence, and stalled or unstable runs.
5. **Collaboration** — give team members a simple GitHub location for daily ML status reports.

## Reading the logs

### Browse daily ClearML reports

Open the newest files under:

```text
reports/clearml/
```

Reports are named by UTC date:

```text
reports/clearml/2026-06-11.md
```

Recommended review order:

1. Start with the **Executive summary**.
2. Check **Active task inventory** for task names, IDs, runtimes, and tags.
3. Review the **Metrics table** for latest vs. best observed values.
4. Read **Operational concerns** for anomalies.
5. Use **Recommended next actions** to decide whether to continue, pause, inspect samples, or debug the run.

### Inspect raw solver logs

Raw solver logs are plain text. Useful search patterns:

```bash
# Training and validation summaries
grep -R "Train Summary\|Valid Summary" 0p7p2 0p8p0A 0p8p1

# Checkpoint saves
grep -R "Checkpoint saved" 0p7p2 0p8p0A 0p8p1

# Model size and model hashes
grep -R "Model size\|Model hash" 0p7p2 0p8p0A 0p8p1

# Dataset split paths
grep -R "Loading audio data split" 0p7p2 0p8p0A 0p8p1
```

## Report format

Daily report files should be written in Markdown and should use this rough structure:

```markdown
# ClearML Daily ML Observer — YYYY-MM-DD

Generated at: <ISO timestamp> (UTC)

## Executive summary

## Active task inventory

## Metrics table

## Operational concerns

## Recommended next actions

## Data availability notes
```

The exact tables may vary depending on what ClearML returns for a given day. When a metric is absent, prefer explicitly stating that it was not reported rather than leaving the field ambiguous.

## Naming conventions

- Daily reports: `reports/clearml/YYYY-MM-DD.md` using UTC dates.
- Raw experiment folders: compact model/experiment identifiers such as `0p7p2`, `0p8p0A`, or `0p8p1`.
- Solver logs: keep original solver naming such as `solver.log.0`, `solver.log.1`, etc.

## Adding a new ClearML daily report

1. Generate the report from ClearML telemetry.
2. Save it as `reports/clearml/YYYY-MM-DD.md`.
3. Verify that the report contains:
   - task names and IDs;
   - status and runtime information;
   - latest and best/min metric values where available;
   - resource notes when available;
   - limitations or missing-metric notes.
4. Commit it with a documentation-style message:

```bash
git add reports/clearml/YYYY-MM-DD.md
git commit -m "docs: add ClearML daily report for YYYY-MM-DD"
git push origin main
```

## Adding raw solver logs

When archiving a new training run:

1. Create a folder named after the experiment/model version.
2. Copy solver logs without rewriting them.
3. Avoid committing large binary artifacts, checkpoints, generated audio, or model weights.
4. Add a short note in this README if the new folder introduces a new experiment family that future readers need to understand.

## Data sensitivity and storage policy

This repository should stay focused on logs and reports. Do **not** commit:

- API keys, access tokens, or `.env` files;
- private credentials or cloud profiles;
- model checkpoints or large binary artifacts;
- generated audio batches unless explicitly intended for archival review;
- personally identifiable information.

Raw logs may contain filesystem paths, task IDs, host-resource metrics, and dataset paths. Review new logs before publishing if repository visibility changes.

## Local setup

No Python environment is required to read or edit this repository.

```bash
git clone https://github.com/Deep-Noise-Labs/ml-logs.git
cd ml-logs
```

Useful optional tools:

- `grep`, `ripgrep`, or your editor search for text logs;
- a Markdown previewer for `reports/clearml/*.md`;
- ClearML UI access when you need to cross-reference task IDs.

## Relationship to other Deep Noise Labs repositories

- [`Deep-Noise-Labs/data-engineering`](https://github.com/Deep-Noise-Labs/data-engineering) contains dataset preparation, annotation, taxonomy, Label Studio, and training-data conversion scripts.
- This repository contains the operational ML logs and daily reports produced around training and evaluation work.

## Maintenance checklist

Use this checklist when updating the archive:

- [ ] File paths follow the existing folder conventions.
- [ ] Markdown tables render correctly on GitHub.
- [ ] Missing metrics are explicitly called out.
- [ ] No secrets or credentials are committed.
- [ ] Large binaries remain out of Git.
- [ ] Commit messages describe the report/log date or experiment being archived.
