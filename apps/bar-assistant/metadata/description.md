# Bar Assistant

Bar Assistant is a self-hosted application for managing your home bar. Add the
ingredients you have on hand and it shows which cocktails you can make, lets you
browse and save recipes, build shopping lists, and organize your collection.

This app bundles four services:

- **Salt Rim** - the web client you open in your browser (main service)
- **Bar Assistant API** - the backend server
- **Meilisearch** - powers fast search and filtering
- **Redis** - caching and session storage

## Browser-reachable URLs (automatic)

Salt Rim runs in your browser and talks to the API and Meilisearch directly, so
those two services are published on host ports (`8586` for the API, `8587` for
search). Their browser-reachable URLs are wired up automatically from your Tipi
host's LAN IP (`${INTERNAL_IP}`) — no manual configuration needed.

This works when you reach Tipi at its LAN IP. If you instead access Tipi through
a custom domain or reverse proxy, Salt Rim's in-browser calls stay pinned to the
LAN IP and won't resolve — that setup would need a compose edit (it isn't
exposed as an install option).

## First launch

Leave **Allow new user registration** on for the first launch, open the app, and
click Register to create your account. The server can take a minute to become
ready on first start.

Source: https://github.com/bar-assistant/docker
