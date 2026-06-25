# Project AI Agent Instructions

This file is the entry point for AI agents working in this repository. It is
**user-owned** — `wmill` never overwrites it. Add your project-specific
guidance below the include line.

The line below pulls in Windmill's managed CLI guidance (skills, deploy flow,
debugging jobs, etc.). Refresh it with `wmill refresh prompts`. Remove the
include line if you don't want the managed guidance in this project.

@AGENTS.wmill.md

## Project-specific instructions

This repo holds the **Windmill workspace content** (scripts, flows, apps) for the
`WORKSPACE_NAME` workspace on https://version2.bazooka.dev/. `wmill.yaml` is at the repo
root, so run all `wmill` commands from here.

### Repo split
- **This repo** — Windmill code only (`f/**`).
- **`bazooka-windmill-version2`** — the platform/infra (ansible, docker-compose,
  Caddyfile) that provisions and runs the Windmill instance. Do not put Windmill
  scripts/flows/apps there.

### Resources & secrets in git: `git_` opt-in convention
Secret *values* are never committed. `wmill.yaml` has `skipSecrets: true`, so secret
variables never sync. `skipResources: false` / `skipVariables: false` let `wmill` write
resource and variable files to disk, but `.gitignore` ignores `*.resource.yaml` /
`*.variable.yaml` by default and only tracks ones whose name has a **`git_` prefix**
(`!**/git_*.resource.yaml`). `*.resource-type.yaml` schemas are always tracked.

To version a resource safely (in this exact order — getting it wrong leaks secrets):
1. In the Windmill UI, mark the resource's sensitive fields **secret** → Windmill moves
   each value into its own secret variable and leaves a `$var:` reference in the resource.
2. Rename the resource so its Windmill name starts with `git_` (or create it that way).
3. `wmill sync pull` → `git_<name>.resource.yaml` now holds only `$var:` refs and is safe
   to commit.

NEVER add the `git_` prefix to a resource that still has plaintext secret fields. If you
(the agent) are asked to commit a resource, first verify it contains only `$var:`
references and no plaintext credentials.

### Deploy mode: git push (CI runs wmill sync push)
`.github/workflows/push-on-merge.yaml` runs `wmill sync push` on push to `main` or
`develop` (feature branches do **not** deploy). Deploy by committing and pushing — do
not run `wmill sync push` by hand unless CI is broken or the user asks to bypass it.
Only code and safe resources are pushed (`--skip-secrets`).

### Sync is one-way (git → Windmill)
By default, Windmill's built-in **Git Sync is not enabled** for new workspaces. Edits
made directly in the Windmill UI are **not** captured in git automatically — run
`wmill sync pull`, commit, and open a PR to bring them back. (The `[WM]` guard in the CI
workflow is there in case Git Sync is enabled later to prevent loops).

### Git flow
`feature/*` → PR → `develop` → release PR → `main`. All deploys target the same single
workspace (`WORKSPACE_NAME`); there is no separate staging instance.
