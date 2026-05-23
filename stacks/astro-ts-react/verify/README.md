<!--
SPDX-FileCopyrightText: 2026 Avish Jha <avish.j@pm.me>

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Verify astro-ts-react

These files let CI regenerate the template and prove the result still works.

## Files

- `answers.yml` — Copier inputs. Values match the smoke repo so generated names line up.
- `trigger.py` — Edits a smoke-repo PR branch to fire every path-filtered job in the generated `ci.yml`.
- `README.md` — Documents the verify contract and local repro flow.

## Run locally

```bash
uv tool install copier==9.15.1
uv tool install rust-just==1.51.0
copier copy --defaults --vcs-ref=HEAD \
  --data-file stacks/astro-ts-react/verify/answers.yml \
  stacks/astro-ts-react /tmp/astro-ts-react-out
cd /tmp/astro-ts-react-out
git init --quiet && git switch -c verify/preflight
git add -A
just install && just lint && just build
```

## How it runs in CI

On every Blueprints PR, `verify-stack.yml`:

1. Regenerates the template, initializes a temporary `verify/preflight` git repo, and runs `just install`, `just lint`, `just build`.
2. Force-pushes the regenerated project to `main` of `blueprints-smoke-astro-ts-react` to reset the baseline (no CI is awaited on this push).
3. Opens a trivial PR there using `trigger.py` and waits for PR CI — this is the smoke signal.
4. Posts a summary back to the Blueprints PR.

`weekly.yml` runs the same flow against `main` on a weekly cron to catch drift from upstream changes.

## Troubleshooting

- **PR CI failed**: open the linked workflow run and use the Blueprints smoke summary comment to jump to the smoke PR URL. Cleanup runs `if: always()`, so the verify PR may already be closed after the run.
- **Stale verify branches**: cleanup runs `if: always()`, so stale branches mean the cleanup step itself failed — check job logs.

## PAT rotation

`SMOKE_TOKEN` is a fine-grained PAT scoped to `blueprints-smoke-*` with Contents R/W, Pull requests R/W, Actions R, Metadata R. Expires yearly; rotate via GitHub -> Settings -> Developer settings -> Fine-grained tokens.
