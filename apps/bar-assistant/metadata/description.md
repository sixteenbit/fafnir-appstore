# Bar Assistant

Bar Assistant is a self-hosted application for managing your home bar. Add the
ingredients you have on hand and it shows which cocktails you can make, lets you
browse and save recipes, build shopping lists, and organize your collection.

This app bundles four services:

- **Salt Rim** - the web client you open in your browser (main service)
- **Bar Assistant API** - the backend server
- **Meilisearch** - powers fast search and filtering
- **Redis** - caching and session storage

## Browser-reachable URLs

Salt Rim runs in your browser and talks to the API and Meilisearch **directly**,
so both must be reachable from your browser. They're published on host ports
(`8586` for the API, `8587` for search).

**LAN use (default):** leave the two URL override fields blank — the API/search
URLs are filled in automatically from your Tipi host's LAN IP (`${INTERNAL_IP}`).
Nothing to configure.

**Internet use:** because Salt Rim, the API, and Meilisearch are three separate
origins, each needs its own public HTTPS address — Runtipi's single app domain
only covers Salt Rim. Give the API and search their own hostnames and set the
two override fields to those URLs:

1. Expose the Tipi app domain to Salt Rim as usual.
2. Point two more public hostnames at the Tipi host's ports `8586` and `8587`.
   With a **Cloudflare Tunnel** (wildcard), add two public hostnames, e.g.
   `ba-api.example.com → http://<tipi-host>:8586` and
   `ba-search.example.com → http://<tipi-host>:8587`.
3. Set **API URL override** = `https://ba-api.example.com` and
   **Meilisearch URL override** = `https://ba-search.example.com`, then save.

All three must be HTTPS — a mix of an HTTPS page calling an HTTP/LAN address is
what triggers the browser's "access your local network" prompt and mixed-content
blocking.

## First launch

Leave **Allow new user registration** on for the first launch, open the app, and
click Register to create your account. The server can take a minute to become
ready on first start.

Source: https://github.com/bar-assistant/docker
