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

## System Dependencies

Install these before running the pipeline:

- Python 3.10+
- `git`
- `scc` for lines-of-code metrics in `codebase_profiler`
- Node.js / `npx` for duplication metrics in `codebase_profiler`

macOS:

```bash
brew install git scc node
```

Windows with Chocolatey:

```powershell
choco install git nodejs scc -y
```

If Chocolatey fails for `scc`, download the Windows binary directly from the
[`scc` releases page](https://github.com/boyter/scc/releases/latest), extract
`scc.exe`, and put it on your `PATH`.

Ubuntu/Debian:

```bash
sudo apt-get update
sudo apt-get install -y git nodejs npm
# Install scc from https://github.com/boyter/scc/releases/latest
```

Verify:

```bash
git --version
scc --version
node --version
npx --version
```

## Python Setup

```bash
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
pip install -e ./codebase_profiler
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
