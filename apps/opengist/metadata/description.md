# Opengist

Opengist is a self-hosted pastebin **powered by Git** — a lightweight,
personal alternative to GitHub Gist. Every gist is a real Git repository, so
you can clone, pull, and push your snippets like any other repo.

## Features

- Create public, unlisted, or private gists with syntax highlighting
- Markdown and CSV rendering, plus embeddable gists
- Revisions for every gist (it's Git under the hood)
- Full-text search across your snippets
- Login with GitHub, GitLab, Gitea, or any OpenID Connect provider
- Lightweight single binary — no external database required

## Configuration

- **Public URL** — derived automatically from your Runtipi domain (the LAN
  `host:port`, or your exposed domain over HTTPS), so OAuth logins, embeds, and
  clone URLs resolve correctly out of the box. The override field is only needed
  if you front Opengist with an external reverse proxy or tunnel whose hostname
  differs from the domain Runtipi knows.
- **Session secret key** — auto-generated; leave as-is. Pinning it keeps
  sessions valid across restarts and updates.
- **Advanced settings** (OAuth providers, SSH git server, alternate databases)
  are not surfaced as install fields. Configure them with `OG_*` environment
  variables or by mounting a `config.yml` — see the
  [configuration docs](https://opengist.io/docs/configuration/configure.html).

## Notes

- App data (the SQLite database, repositories, and config) is persisted under
  the app data directory at `/opengist`.
- Files are written as UID/GID `1000:1000`.
- **First run:** the first account you register becomes the admin. You may want
  to disable open registration afterwards from the admin settings.
- **Cloning over SSH:** Opengist also supports a built-in SSH server (container
  port `2222`) for `git` clone/push over SSH. It is not exposed by this app by
  default; HTTP cloning works out of the box. See the
  [Opengist documentation](https://opengist.io/docs/) for configuration options
  (OAuth, SSH, custom settings) via environment variables.
