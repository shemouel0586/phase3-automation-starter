# Phase 3: Automation — Helm, Git, CI/CD (GitHub Actions)

This starter meets the assignment requirements:
- **Helm chart** under `helm/myapp` for a simple Flask service.
- **Git strategy** documented below.
- **CI/CD** with GitHub Actions:
  - `ci.yml`: matrix lint+test (pylint, pytest) on Python 3.10/3.11/3.12, then build & push Docker image to **GHCR** on `main`.
  - `release-chart.yml`: package Helm chart and **push to GHCR (OCI)** when you tag a release `chart-v*`.
  - `deploy.yml` (optional): deploy with Helm if `KUBECONFIG_B64` secret is set.

## Quick Start

```bash
# run locally
python -m venv venv && source venv/bin/activate
pip install -r app/requirements.txt
python app/main.py
# http://localhost:8080/
```

### Helm locally
```bash
helm lint helm/myapp
helm template test ./helm/myapp
```

### Publish chart to GHCR (artifact repository)
1. Edit `helm/myapp/values.yaml` -> set `image.repository` to `ghcr.io/<OWNER>/<REPO>/myapp`.
2. Commit, push.
3. Create tag to trigger chart release:
```bash
git tag chart-v0.1.0
git push origin chart-v0.1.0
```
The workflow will package the chart and push to `oci://ghcr.io/<OWNER>/<REPO>`.

### CI matrix with pylint
- See `.github/workflows/ci.yml`: runs pylint on 3 Python versions.

## Branching strategy (documented for deliverable)
- `main`: protected; release-ready.
- `develop`: integration branch.
- `feature/*`: short-lived branches, PR into `develop`.
- `release/*`: stabilize; merge to `main` + tag.
- `hotfix/*`: fixes from `main` back to `develop`.

### Typical workflow
```
feature/* -> PR to develop -> squash merge
develop -> release/* -> tests -> merge to main + tag
```

### Conflict resolution (quick guide)
1. `git fetch origin`
2. `git checkout feature/my-change`
3. `git merge origin/develop` (or rebase)
4. Fix conflicts, `git add -A`, `git commit`
5. Push updates to PR

## Secrets you may set in repo Settings → Secrets and variables → Actions
- `KUBECONFIG_B64` — base64 of kubeconfig (for optional deploy workflow).

## Deliverables checklist
- [x] Helm chart published to GHCR (OCI) via `release-chart.yml`
- [x] CI matrix with pylint (3.10–3.12)
- [x] Branching strategy + PR workflow documented in this README
- [x] Optional deploy workflow included
