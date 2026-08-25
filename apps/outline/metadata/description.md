# Outline

A fast, collaborative knowledge base for your team, built with React and Node.js. A
solid self-hosted alternative to Notion, Confluence, or Obsidian Publish.

## Features

- Rich Markdown editor with slash commands, embeds, and real-time collaboration
- Nested collections and documents, with full-text search
- Comments, mentions, and document history
- REST API, webhooks, and integrations (Slack, GitHub, Figma, and more)
- Import from Notion, Confluence, and Markdown

## What's in this bundle

Five services, mirroring the reference Outline deployment:

| Service            | Role                                                       |
| ------------------ | ---------------------------------------------------------- |
| `outline-nginx`    | Single public entrypoint. Serves Outline at `/` and the login provider at `/uc` |
| `outline`          | Outline itself                                             |
| `outline-oidc`     | Small built-in OIDC provider that holds your login account |
| `outline-postgres` | Document storage                                           |
| `outline-redis`    | Websockets, background jobs, and cache                     |

Only nginx is reachable from outside. Everything else talks over the app's private
network.

## Install notes

**This app must be exposed on its own domain over HTTPS.** It's marked
`force_expose`, so Runtipi will require a domain at install. Outline builds every
absolute link and the OIDC redirect URI from that domain, so it cannot run as a
plain LAN app on an IP and port.

**Sign-in.** Outline has no local account signup, so this bundle ships a small OIDC
provider to supply one. At install you set a **Username** and **Password**; those
become your first Outline account. On the Outline login page choose **OpenID**,
which hands you to the provider at `/uc`, and sign in there. Every other form field
is auto-generated and should be left alone.

**First start is slow.** Outline runs database migrations on boot, so the first
launch can take a couple of minutes before the page responds. Postgres and the OIDC
provider also need to initialize. If you get a 502 from nginx right after
installing, give it a minute and reload.

**Changing the domain later** means the OIDC redirect URI recorded at install no
longer matches, and login will fail. The redirect URI is seeded once into
`app-data/<store>/outline/data/oidc-config.json`; either edit that file and restart,
or reinstall the app under the new domain.

## Running two Outlines side by side

Runtipi namespaces installs by app store, so this app can run at the same time as
the Outline from the official Runtipi store. They get separate data directories
(`app-data/<store>/outline`), separate Docker networks, and separate Compose
projects, so nothing is shared between them. Two things you do need to set
differently for the second install:

- **A different domain.** Each instance needs its own hostname.
- **A different host port.** This app defaults to `8405`; the official one defaults
  to `8404`.

They are fully independent: separate databases, separate accounts, separate content.

Learn more at the official [website](https://getoutline.com).
