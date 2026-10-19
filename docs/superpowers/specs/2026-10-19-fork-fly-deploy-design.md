# Fork Fly Deployment Guard

## Goal

Prevent the Fly.io deployment job from failing in forks that do not have the
upstream repository's `FLY_TOKEN`, while preserving the upstream deployment
workflow.

## Design

Add a job-level condition to `.github/workflows/deploy.yml` that runs the
`deploy` job only when `github.repository` is `sosedoff/pgweb`.

Pushes to `master` in the upstream repository will continue deploying to
Fly.io. In every fork, GitHub Actions will mark the deployment job as skipped.
The checks, Docker build, and release workflows are unchanged.

## Verification

Validate the workflow syntax and inspect the resulting diff. The condition
must be attached to the `deploy` job and must not change its existing trigger
or steps.
