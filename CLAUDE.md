# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

This is a personal [Runtipi](https://runtipi.io) app store — it packages self-hosted apps that aren't in the official Runtipi store. Each app under `apps/<id>/` is validated against `@runtipi/common` schemas. See `@README.md` for the full repo layout and add-an-app walkthrough.

## Commands (use Bun, not npm/yarn)

- `bun install` — install deps
- `bun test` — validate every app's `config.json` + `docker-compose.yml` against the runtipi schemas (`__tests__/apps.test.ts`). Run this after any change under `apps/`.

## App structure

Each app lives in `apps/<id>/` and must contain:
- `config.json` — app metadata. The `id` field **must equal the folder name**.
- `docker-compose.yml` — dynamic compose, `schema_version: 2`.
- `metadata/description.md` and `metadata/logo.jpg` (square 1:1). Good source for app logos: https://dashboardicons.com/.

When scaffolding a new app, copy `apps/whoami/` (minimal) or `apps/bar-assistant/` (multi-service with form fields) as the template.

## Schema gotchas (the validator enforces these — `bun test` catches them)

- Pin image tags — **never** `latest` (Renovate tracks pinned tags).
- `x-runtipi.internal_port` must be a **number**, not a string.
- The top-level `x-runtipi` block requires `overrides: []` (empty array is fine) and `schema_version: 2`.
- Mark exactly one service with `x-runtipi: { is_main: true, internal_port: <n> }`.
- `config.json` needs `dynamic_config: true` for schema_version 2 compose.
- Use `${APP_DATA_DIR}` for persistent volume paths and `${ENV_VAR}` for `form_fields` values injected at runtime.
- Browser-reachable services (frontends calling an API directly) need a host `ports:` mapping; internal-only services (e.g. Redis) do not.

## Version updates

`tipi_version` (integer) is bumped automatically — Renovate detects a new pinned image tag and runs `scripts/update-config.ts <compose-file> <newVersion>`, which increments `tipi_version`, sets `version`, and stamps `updated_at`. Don't hand-edit these for routine image bumps.

## Workflow

Each change goes on a feature branch and merges via PR — GitHub Actions runs `bun test` on every push and PR. Don't commit directly to `main`.
