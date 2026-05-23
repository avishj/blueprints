<!--
SPDX-FileCopyrightText: 2026 Avish Jha <avish.j@pm.me>

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setup

## Prerequisites

- [ ] Bun installed
- [ ] Copier available (`uvx copier@9.15.1`)

## Scaffold (interactive)

```bash
git clone https://github.com/avishj/blueprints /tmp/blueprints
uvx copier@9.15.1 copy /tmp/blueprints/stacks/astro-ts-react my-project --trust
rm -rf /tmp/blueprints
```

Copier will prompt for:

| Variable | Description | Example |
| --- | --- | --- |
| `project_name` | Project/package name | `my-app` |
| `owner` | GitHub owner | `avishj` |
| `description` | One-line summary | `A modern Astro + React app.` |
| `author_name` | Author display name | `Avish J` |
| `author_email` | Author email | `avish.j@pm.me` |
| `copyright_year` | Copyright year | `2026` |
| `env_prefix` | Environment prefix (derived) | `MY_APP_` |

## Locked defaults (not user-prompts)

- Runtime is locked to Bun (`runtime=bun`).
- TypeScript strict mode is locked on (`strict_typescript=true`).

## Derived variables

- `package_name` is derived from `project_name` (lowercased).
- `module_name` is derived from `project_name` (hyphens replaced with underscores).
- `env_prefix` defaults to `{{ module_name | upper }}_` and can be overridden.

## Runtime shell generated files

Branch 2 currently scaffolds:

- `template/package.json.jinja`
- `template/astro.config.mjs.jinja`
- `template/tsconfig.json.jinja`
- `template/src/layouts/BaseLayout.astro.jinja`
- `template/src/pages/index.astro.jinja`
- `template/src/components/App.tsx.jinja`
- `template/src/styles/global.css.jinja`
- `template/src/env.d.ts.jinja`
- `template/src/types/global.d.ts.jinja`
- `template/src/lib/config.ts.jinja`
- `template/.vscode/*`
- `template/.devcontainer/*`

## Scaffold (non-interactive)

```bash
git clone https://github.com/avishj/blueprints /tmp/blueprints
uvx copier@9.15.1 copy /tmp/blueprints/stacks/astro-ts-react my-project \
  --trust \
  --data project_name=my-app \
  --data owner=avishj \
  --data description="A modern Astro + React app." \
  --data author_name="Avish J" \
  --data author_email="avish.j@pm.me" \
  --data copyright_year="2026"
rm -rf /tmp/blueprints
```
