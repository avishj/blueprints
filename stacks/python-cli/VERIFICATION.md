<!--
SPDX-FileCopyrightText: 2026 Avish Jha <avish.j@pm.me>

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Verification

## AI Scope

### Section 1 — Project Metadata (`pyproject.toml`)

**Build & packaging:**

- [ ] Build system uses hatchling (`[build-system]` matches template)
- [ ] `project.name` is the actual app name (no `myapp` remnants)
- [ ] `project.description`, `authors`, `keywords` are filled in (not template placeholders)
- [ ] `project.license` and matching `classifiers` entry are consistent
- [ ] All template `classifiers` retained (`Console`, `Typed`, Python versions, etc.)
- [ ] `project.scripts` entry point: key = app name, value = `myapp.cli:entrypoint` (replace `myapp`)
- [ ] All 5 `project.urls` point to the correct repo (no `myapp` remnants)
- [ ] `dependencies` includes `cyclopts`, `rich`, `pydantic-settings` (plus any project additions)
- [ ] `tool.hatch.build.targets.wheel.packages` points to `src/myapp` (replace `myapp`)

**Dev dependencies:**

- [ ] `[dependency-groups]` `dev` contains all template deps (commitizen, complexipy, mkdocstrings-python, pytest, pytest-cov, pytest-github-actions-annotate-failures, pytest-mock, pytest-randomly, pytest-timeout, pytest-xdist, ruff, ty, zensical)

**Tooling config — must match template exactly:**

- [ ] `[tool.uv]` section present and unchanged (`python-preference = "managed"`)
- [ ] `[tool.ruff]` includes `src = ["src", "tests"]`
- [ ] `[tool.ruff.lint]` uses `select = ["ALL"]` and `ignore = ["COM812", "ISC001"]`
- [ ] `[tool.ruff.lint.per-file-ignores]` includes `tests/**/*.py` relaxations and `tests/e2e/*.py` subprocess exceptions
- [ ] `[tool.ruff.lint.pydocstyle]` — google convention
- [ ] `[tool.pytest.ini_options]` — `addopts`, `filterwarnings`, all 3 markers, `pythonpath`, `testpaths`, `timeout`, `xfail_strict`
- [ ] `[tool.coverage.run]` — `branch`, `core`, `parallel`, `patch`, `relative_files` match template
- [ ] `[tool.coverage.report]` — `exclude_also`, `fail_under`, `show_missing`, `skip_empty` match template

**Tooling config — app-name-dependent (no `myapp` remnants):**

- [ ] `tool.ruff.lint.isort.known-first-party` uses the module name
- [ ] `tool.coverage.run.source` uses the module name
- [ ] `tool.commitizen.version_files` contains only `pyproject.toml:version` (version is no longer duplicated in `__init__.py`)

**Commitizen:**

- [ ] `[tool.commitizen]` section present — `tag_format`, `version`, `version_files`, `update_changelog_on_bump` match template

### Section 2 — Source Code (`src/myapp/`) (replace `myapp`)

- [ ] Directory `src/myapp/` exists (replace `myapp` with your app name)
- [ ] `py.typed` marker file exists (empty file, copy 1:1)

**`__init__.py`:**

- [ ] Module docstring updated to app name
- [ ] `__version__` defined via `importlib.metadata.version("app-name")` — single source of truth in `pyproject.toml`, no hardcoded version string

**`__main__.py`:**

- [ ] Docstring references app name (Allow running as `python -m myapp`) (replace `myapp`)
- [ ] Imports `app` from `myapp.cli` (replace `myapp`)
- [ ] Calls `app.meta()`

**`cli.py`:**

- [ ] Imports `__version__` from `myapp`, `settings` from `myapp.config`, `ExitCode` from `myapp.exit_codes`, `setup_logging` from `myapp.logging` (replace `myapp`)
- [ ] Module-level `logger = logging.getLogger(__name__)` defined
- [ ] `App()` created with `name=`, `help=`, `version=__version__`, `version_flags=["--version", "-V"]`
- [ ] `app.register_install_completion_command()` called to enable completion install command
- [ ] `console = Console()` instantiated
- [ ] `@app.meta.default` function exists — wires `--verbose` flag to `settings.verbose`, calls `setup_logging()`, and calls `app(tokens)`
- [ ] `entrypoint()` function exists — calls `app.meta()` (this is the `console_scripts` target)
- [ ] No `myapp` strings remain in `App(name=...)`, `App(help=...)`, or docstrings
- [ ] Demo command (`hello`) may be replaced, but at least one `@app.command` should exist
- [ ] Commands return `ExitCode` (cyclopts' default `result_action` maps return values to `sys.exit(n)`)
- [ ] Commands use `logger.debug()` for verbose output instead of manual `if settings.verbose` checks

**`config.py`:**

- [ ] `LogFormat` defined as `StrEnum` with `PRETTY` and `JSON` values
- [ ] `Settings` class extends `BaseSettings` with `SettingsConfigDict`
- [ ] `env_prefix` set to `<APP>_` (uppercase app name + underscore, not `MYAPP_`)
- [ ] `env_file` and `env_file_encoding` settings present
- [ ] `verbose: bool = False` field exists (used by CLI `--verbose` flag)
- [ ] `log_format: LogFormat = LogFormat.PRETTY` field exists (set via `<APP>_LOG_FORMAT` env var)
- [ ] Validation errors render a Rich panel and exit with `ExitCode.CONFIG`
- [ ] Module-level `settings = Settings()` instance exported

**`exit_codes.py`:**

- [ ] `ExitCode` enum defined as `IntEnum` with at least `OK = 0`, `ERROR = 1`, `USAGE = 2`, `CONFIG = 78`
- [ ] Docstring documents that cyclopts owns exit code 1 for parse errors and 130 for Ctrl-C

**`logging.py`:**

- [ ] `_JSONFormatter` class defined — formats log records as JSON with `timestamp`, `level`, `logger`, `message` fields
- [ ] `setup_logging()` function defined with `verbose` and `log_format` params
- [ ] `setup_logging()` installs rich tracebacks (`show_locals=verbose`) and suppresses cyclopts frames
- [ ] Pretty mode attaches `RichHandler` with `rich_tracebacks=True` and `show_time`/`show_path` toggled by `verbose`
- [ ] JSON mode attaches `StreamHandler` with `_JSONFormatter`
- [ ] Verbose sets root logger to `DEBUG`, non-verbose sets `INFO`

### Section 3 — Tests

**Directory structure:**

- [ ] `tests/__init__.py` exists (SPDX header only)
- [ ] `tests/unit/__init__.py`, `tests/integration/__init__.py`, `tests/e2e/__init__.py` exist (SPDX header only)
- [ ] All three test tiers present: `unit/`, `integration/`, `e2e/`

**`conftest.py`:**

- [ ] `CliResult` helper class exists with `exit_code`, `output` (stdout), and `errors` (stderr)
- [ ] `fixtures_dir` fixture exists for shared test assets
- [ ] `invoke` fixture exists — wraps `app.meta()` calls with capsys capture and SystemExit handling
- [ ] Imports `app` from `myapp.cli` (replace `myapp`)

**`unit/test_version.py`:**

- [ ] `pytestmark = pytest.mark.unit` set
- [ ] Imports `__version__` from `myapp` (replace `myapp`)
- [ ] Tests that `__version__` is a string and valid semver — these are mandatory, not demo

**`unit/test_config.py`:**

- [ ] `pytestmark = pytest.mark.unit` set
- [ ] Imports `Settings` from `myapp.config` (replace `myapp`)
- [ ] Tests default values, env prefix loading, and that unprefixed env vars are ignored
- [ ] All env var references use `<APP>_` prefix (not `MYAPP_`)

**`unit/test_logging.py`:**

- [ ] `pytestmark = pytest.mark.unit` set
- [ ] Imports `setup_logging` from `myapp.logging` and `LogFormat` from `myapp.config` (replace `myapp`)
- [ ] Tests that pretty mode attaches `RichHandler`
- [ ] Tests that JSON mode emits JSON to stderr
- [ ] Tests that verbose enables `DEBUG`, non-verbose stays `INFO`
- [ ] Uses `autouse` fixture to reset root logger state between tests

**`unit/test_exit_codes.py`:**

- [ ] `pytestmark = pytest.mark.unit` set
- [ ] Imports `ExitCode` from `myapp.exit_codes` (replace `myapp`)
- [ ] Tests that `OK == 0`, `ERROR == 1`, and all codes are `int`

**`integration/test_cli.py`:**

- [ ] `pytestmark = pytest.mark.integration` set
- [ ] Imports `__version__` from `myapp` and `ExitCode` from `myapp.exit_codes` (replace `myapp`)
- [ ] Uses `invoke` fixture from conftest
- [ ] `test_version` — mandatory: asserts `--version` exits `ExitCode.OK` and output contains `__version__`
- [ ] `test_no_args` — mandatory: asserts bare invocation exits `ExitCode.OK` and shows usage
- [ ] `test_hello` — demo (replace with actual command tests)

**`e2e/test_entrypoint.py`:**

- [ ] `pytestmark = pytest.mark.e2e` set
- [ ] `_run()` helper invokes the CLI binary via `subprocess.run` — command name must be app name (not `myapp`)
- [ ] `test_version_flag` — mandatory: subprocess `--version` exits 0
- [ ] `test_no_args_shows_help` — mandatory: bare invocation exits 0 with usage output (or app name)
- [ ] `test_invalid_command` — mandatory: unknown subcommand exits non-zero
- [ ] `test_hello_command` — demo (replace with actual command tests)
- [ ] No `myapp` string remnants in subprocess calls or assertions

### Section 4 — Docs & Community

**`docs/index.md`:**

- [ ] Heading, description, install command, and quick-start command all use app name (no `myapp`)
- [ ] Structure matches template (Installation + Quick start sections at minimum)

**`zensical.toml`:**

- [ ] `site_name`, `site_description` use app name (no `myapp`)
- [ ] `site_url`, `repo_url`, `repo_name` point to correct owner/repo
- [ ] `copyright` has correct year and project name
- [ ] `edit_uri` is `edit/main/docs/`
- [ ] All remaining config (theme, plugins, markdown_extensions, nav) matches template exactly

**`README.md` (template):**

- [ ] All badge URLs use correct owner/repo (no `myapp` remnants in GitHub, PyPI, Docker, Scorecard URLs)
- [ ] SonarCloud badge uses correct project key (auto-filled by Copier from `sonar_project_key`)
- [ ] Description updated (not `"A CLI application."`)
- [ ] Features section retained (Cyclopts, Rich, pydantic-settings, py.typed, Python 3.13+)
- [ ] Install commands use app name (uv, pip, docker)
- [ ] Usage examples updated for actual commands (demo `hello` command replaced)
- [ ] Development section: clone URL, `cd` dir use correct repo name
- [ ] Common tasks table matches template (`just lint`, `just test`, `just build`, `just docs`, `just ci`, `just clean`)
- [ ] Configuration section: env var prefix and table use `<APP>_` (not `MYAPP_`), rows updated for actual settings
- [ ] Documentation link points to correct GitHub Pages URL
- [ ] License section matches `pyproject.toml` license choice
- [ ] Star History chart URL uses correct owner/repo

**`CONTRIBUTING.md`:**

- [ ] Clone URL and `cd` dir use correct repo name (no `myapp`)
- [ ] Issues link at bottom uses correct owner/repo
- [ ] All remaining content (workflow steps, guidelines, IDE integration, security ref) matches template

**`SECURITY.md`:**

- [ ] Advisory link uses correct owner/repo (no `myapp`)
- [ ] Rest of file matches template exactly

**`CHANGELOG.md`:**

- [ ] File exists — copy 1:1 from template (commitizen manages content)

**`LICENSE`:**

- [ ] File present — copy 1:1 from template (AGPL-3.0) unless a different license is chosen
- [ ] If license changed: replace file entirely, and update `pyproject.toml` license field, classifiers, README badge, and README footer to match

**Inline SPDX headers (all commentable files):**

- [ ] Every commentable file (`.py`, `.yml`, `.yaml`, `.toml`, `.md`, `.properties`, `Dockerfile`, `justfile`, `CODEOWNERS`, dotfiles) has `SPDX-FileCopyrightText` and `SPDX-License-Identifier` in a comment header
- [ ] Copyright holder and year are updated (not `Avish J <avish.j@pm.me>` unless that is the actual holder)
- [ ] License identifier matches the project license (AGPL-3.0-or-later unless changed)

**`REUSE.toml`:**

- [ ] `version = 1` present
- [ ] Covers only uncommentable files: `*.json`, `*.lock`, `src/myapp/py.typed` (replace `myapp` with your actual package name)
- [ ] `SPDX-FileCopyrightText` entries use actual copyright holder (not `Avish J <avish.j@pm.me>`)
- [ ] `SPDX-License-Identifier` entries match the project license (AGPL-3.0-or-later unless changed)
- [ ] `LICENSES/**` annotation present with FSF copyright

**`LICENSES/` directory:**

- [ ] `LICENSES/AGPL-3.0-or-later.txt` exists with full license text (not a placeholder)
- [ ] If license changed: corresponding `LICENSES/<SPDX-ID>.txt` file exists, old one removed
- [ ] No extra license files for licenses not used by any file

**`CODEOWNERS`:**

- [ ] File exists with `* @<owner>` or another valid ownership entry

### Section 5 — CI/CD Workflows (`.github/workflows/`)

**Reusable workflows (copy 1:1 from template):**

- [ ] `_codeql.yml` — CodeQL analysis with `security-and-quality` queries, paths-ignore for docs/tests, threat models configured, and remote setup action usage
- [ ] `_osv-scanner.yml` — OSV dependency vulnerability scanner with CI differential scanning and weekly full-repo scanning modes, SARIF upload
- [ ] `_security.yml` — Semgrep SAST, Trivy filesystem scan, Gitleaks secret scan, zizmor workflow security scan
- [ ] `_trivy-image.yml` — reusable Trivy container image scan with SARIF upload

**Shared setup action (hosted in Blueprints repo):**

- [ ] All workflow jobs reference a pinned commit of `avishj/blueprints/stacks/python-cli/actions/setup` (currently `@19bdb966ebc356d5b517946bc707c4d9e07f8361`, no local `.github/actions/` directory in the project)
- [ ] No `.github/actions/` directory exists in the project — the setup action is centralized in the Blueprints repo at `stacks/python-cli/actions/setup/action.yml`

**`ci.yml`:**

- [ ] All 12 jobs present: `changes`, `checks`, `docker`, `test`, `sonarcloud`, `package`, `complexity`, `security`, `codeql`, `osv-scanner`, `dependency-review`, `ci-passed`
- [ ] `changes` job and `ci-passed` gate job are present, and `ci.yml` otherwise matches the template including remote setup action usage and current job gating
- [ ] `sonarcloud` job uses `environment: sonarcloud`
- [ ] `docker` job — `docker build -t` and `docker run --rm` image name uses app name (not `myapp`)
- [ ] `package` job — `uv run --with dist/*.whl --no-project --` entry-point verification uses app name (not `myapp`)
- [ ] All remaining `ci.yml` jobs and behavior match the template exactly

**`release.yml`:**

- [ ] Triggered on `v*` tags only
- [ ] `check` job — replace `myapp` in the PyPI URL with app name
- [ ] `publish-pypi` job — environment `url` uses app name (not `pypi.org/p/myapp`)
- [ ] `docker` job — replace `myapp` in the entry-point verification step with app name
- [ ] All remaining `release.yml` jobs and behavior match the template exactly

**`docs.yml`:**

- [ ] Copy 1:1 from template — builds zensical docs, deploys to GitHub Pages
- [ ] Triggers on push to `main` for `docs/**`, `zensical.toml`, `src/**` paths + `workflow_dispatch`
- [ ] All remaining `docs.yml` behavior matches the template exactly

**`weekly.yml`:**

- [ ] Schedule is `cron: "0 0 * * 0"` with `workflow_dispatch`
- [ ] All remaining `weekly.yml` behavior matches the template exactly, copy 1-1.

**`labeler.yml`:**

- [ ] Runs `actions/labeler` on `pull_request_target` with `sync-labels: true`
- [ ] All remaining `labeler.yml` behavior matches the template exactly, copy 1-1.

### Section 6 — GitHub Config (`.github/` non-workflow)

**`settings.yml`:**

- [ ] `repository.name` uses app name (not `myapp`)
- [ ] `repository.description` updated (not still `"A CLI application. Change This"`)
- [ ] `repository.homepage` points to correct GitHub Pages URL (no `myapp`)
- [ ] `repository.topics` updated (keep `python`, `cli`, `cyclopts`; replace `change-this` with relevant topics)
- [ ] All remaining settings (merge strategy, branch protection, labels, status checks) match template exactly

**`FUNDING.yml`:**

- [ ] `github` entry uses correct owner username

**`ISSUE_TEMPLATE/bug_report.yml`:**

- [ ] Version field description references app's `--version` command (not `myapp --version`)
- [ ] Reproduction steps placeholder uses app name (not `myapp ...`)
- [ ] All remaining fields and structure match template

**`ISSUE_TEMPLATE/feature_request.yml`:**

- [ ] Copy 1:1 from template

**`PULL_REQUEST_TEMPLATE.md`:**

- [ ] Copy 1:1 from template

**`labeler.yml` (config):**

- [ ] Copy 1:1 from template — defines labels for `bug`, `feature`, `refactor`, `documentation`, `ci`, `dependencies`, `security`, `tooling` based on branch patterns and changed files

### Section 7 — Container (`Dockerfile`, `.dockerignore`)

**`Dockerfile`:**

- [ ] Copy 1:1 from template — multi-stage build (uv > builder > runtime), non-root user, cache-friendly dep install
- [ ] `ENTRYPOINT` changed to app name (not `myapp`) — only required change

**`.dockerignore`:**

- [ ] Copy 1:1 from template — excludes `.git/`, `.venv/`, tests, docs, build artifacts, config files not needed at runtime

### Section 8 — Dev Tooling & Root Config

**`.vscode/settings.json` (copy 1:1 from template):**

- [ ] `python.defaultInterpreterPath` set to `${workspaceFolder}/.venv`
- [ ] `python.analysis.typeCheckingMode` is `off` (ty handles type checking)
- [ ] `python.testing.pytestEnabled` is `true`
- [ ] `[python]` block: `editor.defaultFormatter` is `charliermarsh.ruff`, `editor.formatOnSave` is `true`
- [ ] `source.fixAll.ruff` and `source.organizeImports.ruff` both set to `explicit`

**`.vscode/extensions.json` (copy 1:1 from template):**

- [ ] All 13 `recommendations` present (ms-python.python, charliermarsh.ruff, astral-sh.ty, nefrob.vscode-just-syntax, GitHub.vscode-github-actions, EditorConfig.EditorConfig, ms-vscode-remote.remote-containers, GitHub.vscode-pull-request-github, usernamehw.errorlens, tamasfe.even-better-toml, DavidAnson.vscode-markdownlint, SonarSource.sonarlint-vscode, crate-ci.typos-vscode)
- [ ] All 6 `unwantedRecommendations` present (autopep8, black-formatter, flake8, isort, pylint, mypy-type-checker)

**`.vscode/launch.json`:**

- [ ] `module` uses app name (not `myapp`)
- [ ] `env` uses correct `<APP>_VERBOSE` prefix (not `MYAPP_VERBOSE`)
- [ ] `args` updated for actual CLI commands (demo `["hello", "world"]` replaced)

**`.devcontainer/Dockerfile` (copy 1:1 from template):**

- [ ] `FROM ghcr.io/astral-sh/uv:...` pinned with `@sha256:` digest
- [ ] `FROM mcr.microsoft.com/devcontainers/python:3.13` pinned with `@sha256:` digest
- [ ] `COPY --from=uv /uv /uvx /usr/local/bin/`
- [ ] `uv tool install rust-just==1.51.0` installs just

**`.devcontainer/devcontainer.json`:**

- [ ] `name` uses app name (not `myapp`)
- [ ] `build.dockerfile` points to `Dockerfile` (no community features)
- [ ] `updateContentCommand` is `uv sync`
- [ ] `postCreateCommand` is `uvx --with pre-commit-uv==4.2.1 pre-commit@4.6.0 install --install-hooks`
- [ ] `forwardPorts` includes `8000`, `portsAttributes` labels it as `Zensical`
- [ ] Extensions list matches `.vscode/extensions.json` minus `ms-vscode-remote.remote-containers`

**Copy 1:1 from template (no changes needed):**

- [ ] `justfile` — all 6 recipes: `lint`, `test`, `build`, `docs`, `ci`, `clean`
- [ ] `.pre-commit-config.yaml` — all 10 repos: pre-commit-hooks, ruff, ty (local), yamllint, validate-pyproject, complexipy, commitizen, typos, reuse, gitleaks
- [ ] `.editorconfig` — indent/charset/line-ending rules for `*`, `*.yml/yaml`, `*.json`, `*.md`, `*.toml`, `*.properties`, `Dockerfile`, `justfile`, `LICENSE`, `LICENSES/**`
- [ ] `.gitattributes` — line-ending normalization, diff drivers, linguist overrides
- [ ] `.gitignore` — Python, dist, venv, testing, linting, type-checking, SonarCloud, docs, IDE, OS, env file patterns
- [ ] `renovate.json` — best-practices config, OSV vulnerability alerts, package rules for GHA/Dockerfile/pre-commit

**`codecov.yml`:**

- [ ] Copy 1:1 from template, then update all 3 flag `paths` entries (`unit`, `integration`, `e2e`) from `src/myapp/` to match your app name
- [ ] All remaining config (precision, range, status targets, comment layout, github_checks) unchanged

**`sonar-project.properties`:**

- [ ] `sonar.projectKey`, `sonar.organization`, and `sonar.projectName` contain correct values (auto-filled by Copier)
- [ ] All remaining config (python version, sources/tests paths, coverage/test report patterns, quality gate, encoding) matches template exactly

### Section 9 — Install & Local Validation

> Run everything at least once after migration — some checks may initially fail if code was ported from a different setup. The goal is to surface all issues early, then fix until everything passes.

**Environment setup:**

- [ ] `uv sync` — venv created, all deps installed
- [ ] `uv lock` — `uv.lock` generated
- [ ] `uvx --with pre-commit-uv==4.2.1 pre-commit@4.6.0 install --install-hooks` — pre-commit and commit-msg hooks active and hook environments are prebuilt

**Quality gates (via justfile):**

- [ ] `just lint` — all pre-commit hooks pass (ruff fix + format, ty, complexipy, validate-pyproject, yamllint, reuse, gitleaks, typos)
- [ ] `just test` — all unit, integration, and e2e tests pass with coverage report
- [ ] `just build` — sdist + wheel built, twine check passes, entry point smoke test passes
- [ ] `just docs` — zensical serves without errors
- [ ] `just ci` — full composite gate (lint + test with coverage ≥ 70% + build + docs build)

### Section 10 — Global Grep Sanity Check

> **Note:** `.copier-answers.yml` contains Copier metadata (template source path, variable values). Exclude it from all grep checks — its contents are expected and managed by Copier.

- [ ] `grep -r --exclude=VERIFICATION.md --exclude=.copier-answers.yml "myapp" .` — zero hits (template app name fully replaced)
- [ ] `grep -r --exclude=VERIFICATION.md --exclude=.copier-answers.yml "MYAPP_" .` — zero hits (env prefix replaced)
- [ ] `grep -r --exclude=VERIFICATION.md --exclude=.copier-answers.yml "Avish J\|avish.j@pm.me" .` — zero hits if author is different; expected hits if author is Avish J
- [ ] `grep -r --exclude=VERIFICATION.md --exclude=.copier-answers.yml "Change This\|change-this" .` — zero hits (settings.yml placeholders replaced)
- [ ] `grep -r --exclude=VERIFICATION.md --exclude=.copier-answers.yml "A CLI application" .` — zero hits (template description replaced)
- [ ] `grep -r --exclude=VERIFICATION.md --exclude=.copier-answers.yml '\${' .` — zero hits (all `${...}` placeholders like `SONAR_PROJECT_KEY`, `SONAR_ORG`, `PROJECT_NAME` resolved)
- [ ] `grep -r --exclude=VERIFICATION.md --exclude=.copier-answers.yml "avishj" . | grep -v "avishj/blueprints"` — zero hits if owner is different; hits referencing `avishj/blueprints` (centralized actions) are expected and excluded by this command
- [ ] `.copier-answers.yml` exists in project root and contains expected answer values

## User Scope

> Cross-reference with [SETUP.md](SETUP.md) and verify all setup steps relevant to your environment are completed. Sections 1–10 above cover file-level verification (AI scope); the items below require manual human action.

### Section 11 — Prerequisites & Local Tools

- [ ] Python 3.13+ installed and available on `PATH`
- [ ] [uv](https://docs.astral.sh/uv/getting-started/installation/) installed
- [ ] [just](https://github.com/casey/just#installation) installed
- [ ] [pre-commit](https://pre-commit.com/#install) installed
- [ ] [Docker](https://docs.docker.com/get-docker/) installed (for container builds and CI docker job)
- [ ] Git configured with commit signing (required by commitizen / sign-off workflow)

### Section 12 — GitHub Repository Setup

**Create and configure the repo:**

- [ ] Create GitHub repo — public, default branch `main`
- [ ] Go to **Settings > Pages** > set Source to **GitHub Actions**

**Add secrets** (Settings > Secrets and variables > Actions > New repository secret):

- [ ] `CODECOV_TOKEN` — get from [codecov.io](https://codecov.io) after adding the repo
- [ ] `DOCKERHUB_USERNAME` — Docker Hub username (only if publishing to Docker Hub)
- [ ] `DOCKERHUB_TOKEN` — Docker Hub access token (only if publishing to Docker Hub)

**Create environment** (Settings > Environments > New environment):

- [ ] Create environment named `sonarcloud` and add `SONAR_TOKEN` as an environment secret
- [ ] Create environment named `pypi`
- [ ] In the `pypi` environment, configure [trusted publisher](https://docs.pypi.org/trusted-publishers/) on PyPI: set repository owner, repo name, workflow `release.yml`, and environment `pypi`

**Enable security features** (Settings > Code security):

- [ ] Enable **secret scanning** with **push protection**
- [ ] Enable **Dependabot security updates** (supplements Renovate for GitHub-native security advisories)

**Enable release immutability:**

- [ ] Enable **release immutability** — Settings > scroll to "Releases" section > select "Enable release immutability" (only applies to future releases)
- [ ] Enable **Docker Hub immutable tags** — Docker Hub > Repositories > select repo > Settings > General > Tag Mutability > select "Specific tags are immutable" > set regex to `^\d+\.\d+\.\d+$` > save (protects exact semver tags while keeping `latest`, major, and major.minor tags mutable)
- [ ] **GHCR immutable tags** — not supported yet; integrity is provided by Cosign signatures and build provenance attestations in the release workflow

**Install the [Renovate GitHub App](https://github.com/apps/renovate):**

- [ ] Grant Renovate access to the repo for automated dependency updates (the `renovate.json` config is already in the template)

**First push:**

- [ ] Push to `main` and verify the CI workflow passes
- [ ] Verify docs deploy to GitHub Pages successfully
- [ ] Verify Renovate creates its onboarding PR
