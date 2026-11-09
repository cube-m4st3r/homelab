# 0005. One repository per container image

- Status: accepted
- Date: 2026-11-09

## Context

Several custom container images are planned, all published to GitHub Container Registry (`ghcr.io/cube-m4st3r/*`):

- `homelab-ee`: Ansible execution environment
- `homelab-toolbox`: dev container with the surrounding tooling
- `utility-tracker`: FastAPI app
- a Prometheus exporter (target still open)

They have different contents, different users and different release rhythms. The question is how to organise their
source and build pipelines.

## Options considered

1. **One monorepo** (e.g. `containers/` with a folder per image and one matrix workflow). One place to look, but
   versioning and changelogs per image get complicated, every change triggers or filters the whole matrix, and the
   repo mixes an Ansible EE with a Python web app.
2. **Images inside the repos that use them** (e.g. the EE inside `homelab`). Fewer repos, but it couples unrelated
   release cycles and makes the images harder to find and reuse.
3. **One repository per image.** Each image has its own README, issues, versions, tags and changelog.

## Decision

**Every image gets its own repository.** Duplication is kept down with shared configuration in the `.github` repo:

- Build, scan, push, SBOM/provenance and cosign signing live in **one reusable workflow** (`container-image.yml`)
  that each image repo calls.
- Linting runs through the shared **`pre-commit.yml`** reusable workflow.
- Renovate settings come from the shared **`renovate-base.json`** preset. Each repo only adds its own rules.
- Each image is versioned independently with SemVer via release-please. Images are tagged `X.Y.Z`, `X.Y`, `X` and
  `sha-<commit>`.

## Consequences

- More repositories to set up: branch rules, Renovate, release-please and GHCR package visibility for each one.
- Each image can be released, documented and rolled back on its own. A broken exporter build never blocks the EE.
- A change to the shared pipeline happens once in `.github` and reaches all images. A breaking change there affects
  all of them, so callers should pin a major tag (`@v1`) once `.github` is released.
- Dependencies between images become explicit: `homelab-toolbox` is built `FROM homelab-ee`, so a breaking EE
  release has to be picked up in the toolbox on purpose.
- Every repo is small and gives a steady stream of visible GitHub activity.
