<!--
SPDX-FileCopyrightText: 2026 Avish Jha <avish.j@pm.me>

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Astro TS React

## What is this stack for?

This stack scaffolds modern frontend projects with a Bun-first workflow, Astro pages, React islands, strict TypeScript, and Tailwind v4 defaults.

It is intentionally maximalist and opinionated for serious project bootstrap quality while preserving clear separation of concerns for future motion (`gsap`) and WebGL (`three`) branches.

## Current runtime shell baseline (Branch 2)

The generated project currently includes:

1. Astro + React integration (`astro.config.mjs` + `@astrojs/react`).
2. Strict TypeScript config with alias paths.
3. Tailwind v4 via `@tailwindcss/vite` and global theme tokens.
4. Base layout + landing page + first React island (`App`).
5. Typed environment stubs and a small config helper.
6. VS Code baseline settings/extensions tuned for Biome-first workflow.
7. Dev Container baseline with pinned toolchain (`bun`, `just`, `pre-commit`).

## Command surface policy

- `package.json` defines runtime/package contract (deps, engines, metadata).
- `justfile` is the single human/operator command surface (added in quality branch).
- Broad script sprawl in `package.json` is intentionally avoided.

## Usage

This stack is a Copier template.

```bash
git clone https://github.com/avishj/blueprints /tmp/blueprints
uvx copier@9.15.1 copy /tmp/blueprints/stacks/astro-ts-react my-project --trust
rm -rf /tmp/blueprints
```

See [SETUP.md](SETUP.md) for variable details and non-interactive scaffolding.
