# Ubooquity

A free, lightweight and easy-to-use home server for your comics and ebooks. Access your library from anywhere with a tablet, e-reader, phone or computer, and stream files over OPDS to any compatible reader app.

## Setup

Ubooquity has two web interfaces:

- **Library** — the main app page Runtipi routes to (container port `2202`). This is what your users browse.
- **Admin** — configuration UI on host port **2203**. Open `http://<your-tipi-ip>:2203/admin/` to add your library folders and users. The linuxserver image serves the pages under `/ubooquity/` (library) and `/admin/` base paths.

On first launch, go to the admin interface to set an admin password and register your comic/ebook folders.

## Libraries

This package mounts two folders from your shared Runtipi media root so they line up with the official media apps:

- `${ROOT_FOLDER_HOST}/media/books` → `/books`
- `${ROOT_FOLDER_HOST}/media/comics` → `/comics`

Point Ubooquity's admin at `/books` and `/comics` when adding libraries.

## Notes

- Runs as `1000:1000` (PUID/PGID) to match files written by the *arr apps.
- **Max memory** is an optional install field — leave it blank for the default 512 MB, or raise it for very large libraries.
