---
name: add-app
description: Scaffold a new Runtipi app in this app store. Creates apps/<id>/ with config.json, docker-compose.yml, and metadata/ following the repo's conventions, then validates with bun test. Use when adding a new app to the store. Takes the app id/name as $ARGUMENTS.
disable-model-invocation: true
---

# Add a new Runtipi app

Scaffold a new app under `apps/<id>/`. The argument is the app id/name (e.g. `/add-app uptime-kuma`). If no argument is given, ask which app to add.

## Steps

1. **Choose the app id.** Lowercase, hyphenated (e.g. `uptime-kuma`). The folder name and the `id` field in `config.json` must be identical.

2. **Create a feature branch** (this repo uses feature-branch + PR; never commit to `main`):
   `git checkout -b add-<id>`

3. **Check the official store for an analog first.** Look in [runtipi/runtipi-appstore](https://github.com/runtipi/runtipi-appstore) (`apps/<id>/`, branch `master`) for an app of the same shape and mirror its conventions — e.g. `bazarr`/`sonarr` for *arr / media-companion apps, `your-spotify` for a browser frontend calling a backend on a host port. Use the modern `x-runtipi` / `schema_version: 2` compose, not their legacy `traefik`-labelled one.

4. **Gather the essentials** — ask the user if not obvious:
   - Docker image + a **pinned** tag (never `latest`)
   - The container's internal port (`internal_port`)
   - A desired host port for the app (`port` in config.json)
   - Whether the app needs config inputs (→ `form_fields`) — but **prefer Runtipi built-in variables over form fields** (see below)
   - Short description, author, source/website URL, categories

   **Use built-in variables, don't ask the user for them:** `${INTERNAL_IP}` for browser-reachable backend URLs, `${ROOT_FOLDER_HOST}/media:/media` for media apps, `${TZ}` for timezone, `${APP_DATA_DIR}` for persistent volumes, `${APP_PORT}` for the user-chosen host port. For linuxserver-style images hardcode `PUID=1000`/`PGID=1000`. Only add a `form_field` for genuinely user-specific values (secrets, API keys, app toggles). See CLAUDE.md → "Runtipi built-in variables" for the full list.

5. **Create the files** under `apps/<id>/`:
   - `config.json` — copy the shape from `apps/whoami/config.json` (simple) or `apps/bar-assistant/config.json` (multi-service / form fields). Set `id` to the folder name, `tipi_version: 1`, `dynamic_config: true`, `available: true`, `supported_architectures: ["arm64", "amd64"]`, and `created_at`/`updated_at` (ms unix timestamps — `date +%s` × 1000).
   - `docker-compose.yml` — `schema_version: 2`. Mark the main/web service with `x-runtipi: { is_main: true, internal_port: <number> }` (internal_port is a **number**). Include the top-level block:
     ```yaml
     x-runtipi:
       schema_version: 2
       overrides: []
     ```
     Pin all image tags. Use `${APP_DATA_DIR}` for persistent volumes and `${ENV_VAR}` for form-field values. Only give host `ports:` to services the browser must reach directly.
   - `metadata/description.md` — a `# Title` heading plus a short description and any install notes.
   - `metadata/logo.jpg` — a square 1:1 logo. https://dashboardicons.com/ is a good source for app icons. If you can't supply one, tell the user they must add `apps/<id>/metadata/logo.jpg` themselves (the test requires the file to exist).

6. **Validate:** run `bun install && bun test`. Fix any schema errors it reports.

7. **Update `README.md`** — add the new app to the Apps table.

8. **Report** what was created and remind the user to add the logo (if missing), then push the branch and open a PR.
