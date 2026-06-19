# Justin's Runtipi App Store

A custom [Runtipi](https://runtipi.io) app store. Add it in your Tipi under
**Settings -> App Stores -> Add App Store** using this repo's URL.

Requires Runtipi `v4.0.0`+ (apps use dynamic compose `schema_version: 2`).

## Apps

| App           | Description                                              |
| ------------- | ------------------------------------------------------- |
| bar-assistant | Self-hosted cocktail / home-bar manager (Salt Rim UI)   |
| unpackerr     | Auto-extracts downloaded archives for Sonarr/Radarr     |
| whoami        | Tiny test app to confirm the store loads                |

## Repository layout

```
apps/
  <app-id>/
    config.json          # app metadata (id MUST match folder name)
    docker-compose.yml   # dynamic compose, schema_version 2 + x-runtipi
    metadata/
      description.md
      logo.jpg           # square 1:1
__tests__/apps.test.ts   # validates every app's config + compose
scripts/update-config.ts # renovate bumps versions via this
```

## Adding a new app

1. Copy `apps/whoami` to `apps/<your-app-id>` and edit the files. The `id` in
   `config.json` must equal the folder name.
2. In `docker-compose.yml`, mark the web service with
   `x-runtipi: { is_main: true, internal_port: <number> }`. Keep
   `internal_port` a **number**, and keep `overrides: []` in the top-level
   `x-runtipi` block - the validator requires both.
3. Pin image tags (no `latest`).
4. `bun install && bun test` to validate locally.
5. Commit, push, then **Update App Stores** in Tipi.

## bar-assistant install notes

Salt Rim runs in your browser and calls the API and Meilisearch directly, so
those are published on host ports. At install set **API URL** and
**Meilisearch URL** to your Tipi's LAN IP, e.g. `http://192.168.1.10:8586`
and `http://192.168.1.10:8587`. Full notes in `apps/bar-assistant/metadata/description.md`.

## Tests

```
bun install
bun test
```

GitHub Actions runs the same suite on every push and PR.

## Notes

- The upstream template's test checked for legacy `docker-compose.json`; this
  store validates modern `docker-compose.yml` with `@runtipi/common`'s
  `dynamicComposeSchemaYaml`.
- License: WTFPL (inherited from the template - change if you prefer).
