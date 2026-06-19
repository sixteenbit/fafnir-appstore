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

**Before scaffolding, check the official store for an analog.** The [runtipi/runtipi-appstore](https://github.com/runtipi/runtipi-appstore) (`apps/<id>/`, branch `master`) is the source of truth for conventions. Find an app of the same shape and mirror it rather than inventing structure — e.g. `apps/bazarr` or `apps/sonarr` for *arr / media-companion apps, `apps/your-spotify` for a browser frontend that calls a backend on a host port. Note: official apps ship both a legacy `docker-compose.yml` (with manual `traefik` labels) and the modern dynamic one; we only use the modern `x-runtipi` / `schema_version: 2` form.

## Runtipi built-in variables (prefer these over form fields)

Runtipi injects these at runtime — use them in `docker-compose.yml` instead of asking the user for the same value via `form_fields`:

- `${INTERNAL_IP}` — the Tipi host's LAN IP. Build browser-reachable backend URLs from this (e.g. `API_URL=http://${INTERNAL_IP}:8586`) instead of a hand-typed URL form field. (Caveat: it's the LAN IP, so installs behind a custom domain/reverse proxy need a public URL instead — see the override pattern below.)
- `${ROOT_FOLDER_HOST}` — the user's configured host storage root. Media apps mount `${ROOT_FOLDER_HOST}/media:/media` so paths line up with the official *arr apps. Don't ask for a host download/media path via a form field.
- `${TZ}` — timezone. Use `TZ=${TZ}`; don't add a TZ form field.
- `${APP_DATA_DIR}` — per-app persistent storage (volumes).
- `${APP_PORT}` — the host port the user picked in the install dialog (the main service is routed automatically via `is_main`/`internal_port`; only reference `${APP_PORT}` for non-main services that also need the chosen port).
- `${APP_DOMAIN}`, `${LOCAL_DOMAIN}`, `${DNS_IP}` — also available; see official apps for usage.

**Optional-override with a built-in default (nested fallback):** when you want a sensible built-in default *and* an optional user override (e.g. auto LAN URL, but overridable with a public URL for internet exposure), make the `form_field` optional and use Compose's nested default substitution:
```yaml
- API_URL=${API_URL:-http://${INTERNAL_IP}:8586}   # blank field → LAN IP; set field → that value
```
Runtipi's renderer is brace-balanced, so the nested `${INTERNAL_IP}` and trailing text resolve correctly — this is the same `${X:-${Y}...}` pattern the official store ships (e.g. mempool/electrs `${BITCOIND_DIR:-${APP_DATA_DIR}/../bitcoind/data}`, invidious `${INVIDIOUS_EXTERNAL_PORT:-${APP_PORT}}`). `bun test` only checks the schema, not interpolation, so confirm new fallback expressions against a real install or a known-good official example.

Conventions worth copying from the official store:
- For linuxserver-style images, set `PUID=1000` / `PGID=1000` (hardcoded, not form fields). Files those apps write are owned by `1000:1000`, so companion daemons should run as that too.
- `config.json` carries `created_at` and `updated_at` (ms unix timestamps).
- Only add a `form_field` for genuinely user-specific values (secrets, API keys, app-specific toggles) — not for anything a built-in variable already provides.

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

For a **manual** functional change (compose edits, form-field changes) with no image bump, do **both together in the same change** — they're a pair, never one without the other:
1. increment `tipi_version` (that's what offers existing installs the update), and
2. stamp `updated_at` to the current ms timestamp (`echo $(( $(date +%s) * 1000 ))`).

Note: our pinned `@runtipi/common` validator requires `overrides: []` in the top-level `x-runtipi` block; the official store omits it. Keep `overrides: []` here so `bun test` passes.

## Workflow

Each change goes on a feature branch and merges via PR — GitHub Actions runs `bun test` on every push and PR. Don't commit directly to `main`.
