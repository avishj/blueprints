# SPDX-FileCopyrightText: 2026 Avish Jha <avish.j@pm.me>
#
# SPDX-License-Identifier: AGPL-3.0-or-later

# v0.11.14
FROM ghcr.io/astral-sh/uv:0.11.14@sha256:1025398289b62de8269e70c45b91ffa37c373f38118d7da036fb8bb8efc85d97 AS uv

# 2.1.8-ubuntu24.04
FROM mcr.microsoft.com/devcontainers/base:2.1.8-ubuntu24.04@sha256:7ee7da33a68d997971660d91ecc8372e55a38a777c3c6bd6808daf91928052db

COPY --from=uv /uv /uvx /usr/local/bin/

USER vscode

HEALTHCHECK --interval=5m --timeout=5s --start-period=10s --retries=3 \
    CMD uv --version
