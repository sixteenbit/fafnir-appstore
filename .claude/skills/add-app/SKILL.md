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

3. **Gather the essentials** — ask the user if not obvious:
   - Docker image + a **pinned** tag (never `latest`)
   - The container's internal port (`internal_port`)
   - A desired host port for the app (`port` in config.json)
   - Whether the app needs config inputs (→ `form_fields`)
   - Short description, author, source/website URL, categories

4. **Create the files** under `apps/<id>/`:
   - `config.json` — copy the shape from `apps/whoami/config.json` (simple) or `apps/bar-assistant/config.json` (multi-service / form fields). Set `id` to the folder name, `tipi_version: 1`, `dynamic_config: true`, `available: true`, `supported_architectures: ["arm64", "amd64"]`.
   - `docker-compose.yml` — `schema_version: 2`. Mark the main/web service with `x-runtipi: { is_main: true, internal_port: <number> }` (internal_port is a **number**). Include the top-level block:
     ```yaml
     x-runtipi:
       schema_version: 2
       overrides: []
     ```
     Pin all image tags. Use `${APP_DATA_DIR}` for persistent volumes and `${ENV_VAR}` for form-field values. Only give host `ports:` to services the browser must reach directly.
   - `metadata/description.md` — a `# Title` heading plus a short description and any install notes.
   - `metadata/logo.jpg` — a square 1:1 logo. https://dashboardicons.com/ is a good source for app icons. If you can't supply one, tell the user they must add `apps/<id>/metadata/logo.jpg` themselves (the test requires the file to exist).

5. **Validate:** run `bun install && bun test`. Fix any schema errors it reports.

6. **Update `README.md`** — add the new app to the Apps table.

7. **Report** what was created and remind the user to add the logo (if missing), then push the branch and open a PR.
