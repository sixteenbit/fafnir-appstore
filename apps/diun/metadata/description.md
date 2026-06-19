# Diun

Diun (**D**ocker **I**mage **U**pdate **N**otifier) periodically checks the
registries behind your running containers and notifies you when a newer image
tag becomes available, so you know when it's time to update.

Diun is **headless** — there is no web UI. It runs in the background, watches
the local Docker daemon through its socket, and sends notifications.

## How it watches

- By default Diun only watches containers labelled `diun.enable=true`.
- Turn on **Watch all containers by default** in the install form to track
  every running container instead.
- The check interval is a cron expression (**Watch schedule**); the default is
  every 6 hours.

## Notifications

Diun supports many notifiers — Discord, Telegram, Gotify, Slack, email,
generic webhooks, and more. Configure them with `DIUN_NOTIF_*` environment
variables or a `diun.yml` config file placed in the app's data directory
(`/data` inside the container). See the
[Diun documentation](https://crazymax.dev/diun/) for the full list and config
reference.

## Notes

- Diun mounts `/var/run/docker.sock` (read-only) to discover your containers.
- Its state (the bbolt database of seen image manifests) is persisted under the
  app data directory at `/data`.
