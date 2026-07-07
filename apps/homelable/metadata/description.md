# Homelable

Homelable is a self-hosted app that visualizes your homelab infrastructure as an interactive network diagram with live status monitoring. Map, monitor and manage the devices and services across your local network on a pan/zoom canvas.

## Features

- **Network discovery & monitoring** — scan your LAN to identify machines, devices and services, with continuous health checks via ping, TCP, HTTP/HTTPS, SSH, Prometheus and health endpoints. Online/offline status renders in real time on the canvas.
- **Smart integrations** — import topology from Zigbee2MQTT, Z-Wave JS UI and Proxmox VE (hosts, VMs, LXC containers), plus MQTT broker connectivity for wireless devices.
- **Visualization & sharing** — customizable node designs with style templates, a read-only public Live View, PNG export, and an interactive pan/zoom canvas.
- **MCP server** — AI clients such as Claude Code and Claude Desktop can read and modify your topology through the bundled MCP server on host port `8001`. It always runs; just ignore it if you don't use AI integrations.

## Architecture

This app bundles three services:

- **frontend** — the nginx web UI (the main service). It reverse-proxies `/api` and the live-status WebSocket to the backend internally.
- **backend** — the API and SQLite datastore. Internal only; reached through the frontend proxy. Runs with the `NET_RAW` capability so ICMP ping checks work.
- **mcp** — the MCP server for AI integrations, exposed on host port `8001`. It always runs; leave it be if you don't connect an AI client.

## First login

The default credentials are **`admin` / `admin`**.

Authentication is **environment-based** — the backend reads `AUTH_USERNAME` / `AUTH_PASSWORD_HASH` from the environment and has no in-app password change. Its default username is `admin`, but the default password hash is empty, which rejects every login — so this package bakes in the bcrypt hash for `admin` to give you a working default.

- **Change the username:** set the *Login username* field when installing.
- **Change the password:** edit the `AUTH_PASSWORD_HASH` value in the app's `docker-compose.yml` to your own bcrypt hash. Because bcrypt hashes contain `$`, each `$` must be doubled to `$$` so Compose renders it correctly. Generate an escaped hash with:

  ```sh
  htpasswd -bnBC 12 "" 'yourpassword' | tr -d ':\n' | sed 's/\$/\$\$/g'
  ```

## Configuration

- **Network scan ranges** — set this to your LAN subnet (JSON array of CIDRs, e.g. `["10.0.0.0/24"]`) so discovery finds your devices. Defaults to `["192.168.1.0/24"]`.
- **Backend secret key**, **MCP API key**, **MCP service key** — auto-generated; leave them as generated. Copy the MCP API key from this app's env if you want to connect an AI client to the MCP server.
- **Allowed browser origins override** — leave blank for LAN access (auto-filled from your host's LAN IP and chosen port). To expose Homelable over the internet, set it to a JSON array with your public HTTPS URL, e.g. `["https://homelable.example.com"]`.

## Source

- Project: <https://github.com/Pouzor/homelable>
