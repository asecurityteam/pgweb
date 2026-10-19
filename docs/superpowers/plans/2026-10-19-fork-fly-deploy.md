# Fork Fly Deployment Guard Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Skip the Fly.io deployment job in forks while preserving deployment from `sosedoff/pgweb`.

**Architecture:** Add a GitHub Actions job-level expression that compares `github.repository` with the upstream repository name. No deployment steps, triggers, secrets, or other workflows change.

**Tech Stack:** GitHub Actions YAML

## Global Constraints

- Run the `deploy` job only when `github.repository` is exactly `sosedoff/pgweb`.
- Leave the workflow trigger and deployment steps unchanged.
- Do not modify the checks, Docker, or release workflows.

---

### Task 1: Guard the Fly.io deployment job

**Files:**
- Modify: `.github/workflows/deploy.yml:12-15`

**Interfaces:**
- Consumes: GitHub Actions `github.repository` context.
- Produces: A skipped `deploy` job in forks and unchanged deployment behavior in `sosedoff/pgweb`.

- [ ] **Step 1: Confirm the guard is absent**

Run:

```bash
rg -F "if: github.repository == 'sosedoff/pgweb'" .github/workflows/deploy.yml
```

Expected: exit status 1 with no output.

- [ ] **Step 2: Add the job-level condition**

Change the start of the job to:

```yaml
jobs:
  deploy:
    name: Deploy to Fly
    if: github.repository == 'sosedoff/pgweb'
    runs-on: ubuntu-latest
```

- [ ] **Step 3: Verify the condition and diff**

Run:

```bash
rg -n -F "if: github.repository == 'sosedoff/pgweb'" .github/workflows/deploy.yml
git diff --check
git diff -- .github/workflows/deploy.yml
```

Expected: the condition appears once under `jobs.deploy`, `git diff --check` exits successfully, and the diff contains only the new condition.

- [ ] **Step 4: Refresh the code graph**

Run:

```bash
graphify update .
```

Expected: graph update completes successfully.

- [ ] **Step 5: Commit**

```bash
git add .github/workflows/deploy.yml
git commit -m "ci: skip Fly deployment in forks"
```
