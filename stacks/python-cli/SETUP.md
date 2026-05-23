<!--
SPDX-FileCopyrightText: 2026 Avish Jha <avish.j@pm.me>

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setup

## Prerequisites

- [ ] Python 3.13+ installed
- [ ] [uv](https://docs.astral.sh/uv/getting-started/installation/) installed
- [ ] [just](https://github.com/casey/just#installation) installed
- [ ] [Docker](https://docs.docker.com/get-docker/) installed (for container builds)
- [ ] Git configured with signing (for commitizen / sign-off)

## Scaffold

Clone the Blueprints repo and run Copier against the `python-cli` stack:

```bash
git clone https://github.com/avishj/blueprints /tmp/blueprints
uvx copier@9.15.1 copy /tmp/blueprints/stacks/python-cli my-project --trust
rm -rf /tmp/blueprints
```

> **Note:** `--trust` is required because the template runs post-scaffold tasks (`uv sync` and `uvx --with pre-commit-uv==4.2.1 pre-commit@4.6.0 install --install-hooks`). Review the tasks in `copier.yml` before running if concerned.
>
> **Why clone first?** Copier discovers its config at the root of the path you give it. Since `copier.yml` lives at `stacks/python-cli/copier.yml` (not the repo root), remote URLs like `gh:avishj/blueprints` won't work directly. Cloning first and pointing to the subdirectory is the supported approach for multi-stack repos.

Copier will prompt for:

| Variable | Description | Example |
| --- | --- | --- |
| `project_name` | PyPI/CLI name (may contain hyphens) | `my-tool` |
| `owner` | GitHub username | `avishj` |
| `description` | Project description | `A CLI application.` |
| `author_name` | Author display name | `Avish J` |
| `author_email` | Author email | `avish.j@pm.me` |
| `copyright_year` | Copyright year for SPDX headers | `2026` |
| `sonar_project_key` | SonarCloud project key | `avishj_my-tool` |
| `sonar_org` | SonarCloud organization | `avishj` |
| `github_topics` | Comma-separated GitHub topics | `python, cli, cyclopts` |
| `env_prefix` | Env var prefix (auto-derived) | `MY_TOOL_` |
| `copyright_holder` | SPDX copyright holder (auto-derived) | `Avish J <avish.j@pm.me>` |

`env_prefix` and `copyright_holder` are auto-derived and shown for optional override. `module_name` is always derived from `project_name` (lowercased, hyphens to underscores) and cannot be overridden.

For non-interactive scaffolding (CI or scripting):

```bash
uvx copier@9.15.1 copy /tmp/blueprints/stacks/python-cli my-project \
  --trust \
  --data project_name=my-tool \
  --data owner=avishj \
  --data description="A CLI application." \
  --data author_name="Avish J" \
  --data author_email="avish.j@pm.me" \
  --data copyright_year="2026" \
  --data sonar_project_key=avishj_my-tool \
  --data sonar_org=avishj \
  --data github_topics="python, cli, cyclopts"
```

## Post-scaffold customization

These items are intentionally not templated by Copier (hardcoded defaults). Change them manually after scaffolding if needed:

**If changing the license** (default: AGPL-3.0-or-later):

- [ ] `pyproject.toml` — `project.license` and matching `classifiers` entry
- [ ] `LICENSE` — replace file with new license text
- [ ] `LICENSES/` — run `reuse download <SPDX-ID>` to add the new license, remove old one
- [ ] `REUSE.toml` — update all `SPDX-License-Identifier` entries
- [ ] All commentable files — update inline `SPDX-License-Identifier` headers
- [ ] `README.md` — license badge and footer link

**If changing the Python version** (default: 3.13):

- [ ] `pyproject.toml` — `requires-python` and Python version classifiers
- [ ] `.pre-commit-config.yaml` — `default_language_version.python`
- [ ] `.github/workflows/ci.yml` — matrix `python-version` list
- [ ] `sonar-project.properties` — `sonar.python.version`
- [ ] `Dockerfile` — base image tag
- [ ] `.devcontainer/Dockerfile` — base image tag

**Other optional changes:**

- [ ] `pyproject.toml` — `project.keywords` (empty list, fill in as desired)

## Validate locally

- [ ] `just lint` — all pre-commit hooks pass (ruff fix + format, ty, complexipy, validate-pyproject, reuse, gitleaks, typos, yamllint)
- [ ] `just test` — all tests pass with coverage report
- [ ] `just build` — sdist + wheel built, twine check passes, entry point smoke test passes
- [ ] `just docs` — zensical serves without errors
- [ ] `just ci` — full composite gate (lint + test with coverage threshold + build + docs build)

## GitHub repo settings

- [ ] Create GitHub repo (public, default branch `main`)
- [ ] Enable GitHub Pages (source: GitHub Actions) for docs
- [ ] Add repo secret: `SONAR_TOKEN`
- [ ] Add repo secrets: `DOCKERHUB_USERNAME`, `DOCKERHUB_TOKEN`
- [ ] Create GitHub environment: `pypi` (with trusted publisher configured)
- [ ] Enable secret scanning with push protection
- [ ] Enable Dependabot security updates
- [ ] Enable release immutability: Settings > scroll to "Releases" section > select "Enable release immutability" (only applies to future releases)
- [ ] Enable Docker Hub immutable tags: Docker Hub > Repositories > select repo > Settings > General > Tag Mutability > select "Specific tags are immutable" > set regex to `^\d+\.\d+\.\d+$` > save (protects exact semver; keeps `latest`/major/minor mutable)
- [ ] GHCR immutable tags: GHCR does not support immutable tags; Cosign signatures and attestations verify provenance by digest but do not prevent tag repointing; consumers must point to digests where possible
- [ ] Push and verify CI workflow passes

## Updating an existing project

When the template changes upstream, pull updates into an existing project:

```bash
git clone https://github.com/avishj/blueprints /tmp/blueprints
cd my-project
uvx copier@9.15.1 update --vcs-ref=<TAG> --trust
rm -rf /tmp/blueprints
```

> Replace `<TAG>` with the desired release tag (e.g. `v1.0.0`). Pinning to a tag ensures you get a known-good snapshot of the template rather than whatever happens to be on `main`.

Copier uses the `.copier-answers.yml` file in your project root to track which template version was used and what answers were given. Do not delete or manually edit this file.
