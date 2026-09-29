# AGENTS.md — plugin-kernel-param

Standalone plugin repo for the host-coupled, compiled-in multi-role
`kernel-param` verb (`verb:kernel-param`). The plugin is a Go module at
`candy/plugin-kernel-param/` (module path
`github.com/opencharly/plugin-kernel-param/candy/plugin-kernel-param`); the root
`charly.yml` only declares `discover: candy` so the repo is a project and its
candy is scanned.

Canonical files:

- `candy/plugin-kernel-param/charly.yml` — the `plugin-kernel-param:` candy
  entity (`plugin:` block, `plan:` check).
- `candy/plugin-kernel-param/` — the Go source: `plugin.go`,
  `schema/kernel_param.cue` (the self-contained `#KernelParamInput`),
  `params/cue_types_gen.go`, `cmd/serve/main.go`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-internals:plugin` — the plugin authoring reference: the `plugin:`
  block, the unified Provider model, the multi-role (`CheckVerbProvider` +
  `ProvisionActor`) contract, the per-plugin CUE-schema contract. Load before
  touching the provider or schema.
- `/charly-check:check` — the declarative check-step surface the `kernel-param:`
  verb is authored through (the check verb catalog).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `go build ./...` in `candy/plugin-kernel-param/` — compile the plugin module.
- `go test ./...` in `candy/plugin-kernel-param/` — the plugin's Go tests.
- `charly box validate` at the repo root — the structural check (the candy +
  `plugin:` block, CUE schema).
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate.
- The changed path is exercised by any check bed composing the `kernel-param:`
  verb.

## Modify this repo

- Edit the `plugin-kernel-param:` candy entity, the Go source, and
  `schema/kernel_param.cue` **together** — the schema is the single source for
  the verb's `params/` struct.
- The package is `kernelparam` (a hyphen is not a legal Go package name); the
  reserved verb word stays `kernel-param`.
- Keep the CHECK (read `/proc/sys`) and ACT (`sysctl -w`) paths in step.

## Landing

- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Load
  `/charly-internals:git-workflow` before any git/PR action; history lives in
  `CHANGELOG/`. Do not restate its rules here.
