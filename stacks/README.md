<!--
SPDX-FileCopyrightText: 2026 Avish Jha <avish.j@pm.me>

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Stacks

Each stack is a [Copier](https://copier.readthedocs.io/) template. Scaffold a new project with:

```bash
git clone https://github.com/avishj/blueprints /tmp/blueprints
uvx copier@9.15.1 copy /tmp/blueprints/stacks/<stack-name> my-project --trust
rm -rf /tmp/blueprints
```

Supported:

- [python-cli](./python-cli/): CLI tools, scripts and command-line applications built with Python.
- [astro-ts-react](./astro-ts-react/): Content-driven websites and apps using Astro with React islands and TypeScript.
