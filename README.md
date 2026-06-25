# cogosite-build

Public build & deploy pipeline for [`thewei/cogosite`](https://github.com/thewei/cogosite) (private source).

## Why this repo exists

The `cogosite` repo is private and holds the source. This public repo carries only the GitHub Actions workflow — no source, no secrets in the repo itself. All credentials come from GitHub Actions secrets.

## What it does

Triggered via `workflow_dispatch` (by cogosite's CI on push to `main`/`dev`, or manually from the Actions UI). It checks out cogosite at the requested ref, installs pnpm deps, builds the Nuxt `.output`, ships it to the server, and verifies by polling `/api/__version` until the running container reports the new SHA.

## Required secrets

| Secret | Purpose |
|---|---|
| `GIT_PULL_TOKEN` | PAT with `Contents: read` on `thewei/cogosite` (and `Actions: write` on this repo if cogosite's CI dispatches via `gh workflow run`) |
| `DEPLOY_HOST` | Server host the workflow SSH-deploys to |
| `DEPLOY_SSH_KEY` | SSH private key authorized on the server |

## Auto-deploy matrix

| Push to | Target env | Auto-deploy? |
|---|---|---|
| `main` | `prod` | yes |
| `dev` | `dev` | yes |
| any other branch | (none) | build only, no upload |

## Manual trigger

Actions → "Build and Deploy" → Run workflow → fill:

- `ref` — branch, tag, or SHA in cogosite (e.g. `main`)
- `env` — `prod` or `dev`
- `deploy` — whether to upload + deploy after build (default `true`)
