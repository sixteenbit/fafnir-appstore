# ChronoFrame

ChronoFrame is a self-hosted personal gallery for managing and sharing photos.
It parses EXIF metadata, supports Live/Motion Photos, recognizes shooting
locations, and presents your library on a timeline and an explore map with a
WebGL photo viewer.

## Install notes

- Sign in with the **admin email** and **admin password** you set in the
  install form. The first account is created on startup.
- Photos and the app database are stored locally under the app's data
  directory (`local` storage provider). S3 and OpenList backends are supported
  by ChronoFrame if you prefer remote storage; set their `NUXT_PROVIDER_*`
  environment variables via a compose override.
- Maps default to MapLibre and work without any API key. To use Mapbox
  instead, supply a Mapbox token via environment overrides.
- Optional GitHub OAuth login is available via ChronoFrame's
  `NUXT_OAUTH_GITHUB_*` environment variables.
