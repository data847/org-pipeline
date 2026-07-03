# Org Pipeline

Runnable package for the LH2 org/repo analysis pipeline.

This repo contains the master orchestrator plus the local subprojects it calls during a full run:

- `codebase_profiler/`
- `lh2-datalabs-eval-kit/`
- `repo-quality-score/`
- helper scripts for merged PR counts and PR task-profile classification

## What It Runs

`run_org_pipeline.py` runs these phases:

1. Merged PR/MR counts
2. PR task-profile report
3. Codebase profiler vendor sheet
4. LH2 eval-kit repository evaluation
5. Sealed repo quality score and org rollup

Use `run_org_pipeline_no_quality.py` when you want to skip the sealed repo-quality-score stage.

## Setup

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
pip install -e ./codebase_profiler
```

Install external CLI tools:

```bash
# macOS
brew install git scc node

# Windows: install git + node, then install scc via Chocolatey or direct binary
choco install scc -y
```

## Tokens

Copy `tokens.example` to `tokens` and fill in the values:

```ini
github-data-token=...
gitlab_token=...
openai_key=...
```

Do not commit real tokens. `tokens` is ignored by git.

## Run Examples

GitHub org:

```bash
python run_org_pipeline.py --github-org <ORG_NAME> --tokens-file tokens --workers 10
```

Single GitHub repo:

```bash
python run_org_pipeline.py --github-repo <OWNER>/<REPO> --tokens-file tokens --workers 1
```

GitLab group:

```bash
python run_org_pipeline.py --gitlab-group <GROUP_NAME> --tokens-file tokens --workers 10
```

Single GitLab project:

```bash
python run_org_pipeline.py --gitlab-project <GROUP>/<PROJECT> --tokens-file tokens --workers 1
```

Local repos folder:

```bash
python run_org_pipeline.py --local-repos-dir ./repos --repos-manifest repos-manifest.example.json --tokens-file tokens --workers 4
```

No-quality variant:

```bash
python run_org_pipeline_no_quality.py --github-org <ORG_NAME> --tokens-file tokens --workers 10
```

## Outputs

Runs are written under:

```text
outputs/org-pipeline-runs/
```

Each run produces a timestamped folder, logs, CSV/JSON/XLSX outputs, and a zip archive.

## More Docs

See `ORG_PIPELINE_README.md` for the full pipeline documentation and `PR_TASK_PROFILE_README.md` for PR classification details.
