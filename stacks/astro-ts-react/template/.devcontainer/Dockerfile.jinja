# syntax=docker/dockerfile:1

# uv v0.11.14
FROM ghcr.io/astral-sh/uv:0.11.14@sha256:1025398289b62de8269e70c45b91ffa37c373f38118d7da036fb8bb8efc85d97 AS uv

# bun v1.3.14
FROM oven/bun:1.3.14@sha256:e10577f0db68676a7024391c6e5cb4b879ebd17188ab750cf10024a6d700e5c4 AS bun

# 2.1.8
FROM mcr.microsoft.com/devcontainers/base:2.1.8@sha256:e389149057371298eaeb72e736fc2c4dcb79974de222dd471d2e6d675d2f61c4

COPY --from=uv /uv /uvx /usr/local/bin/
COPY --from=bun /usr/local/bin/bun /usr/local/bin/bunx /usr/local/bin/

# rust-just 1.51.0
RUN UV_TOOL_BIN_DIR=/usr/local/bin UV_TOOL_DIR=/opt/uv-tools uv tool install rust-just==1.51.0

USER vscode

HEALTHCHECK --interval=5m --timeout=5s --start-period=10s --retries=3 \
    CMD uv --version && bun --version && just --version
