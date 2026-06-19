# Unpackerr

Unpackerr is a lightweight daemon that runs on your download host and
**automatically extracts completed archives** (rar, zip, etc.) so your media
manager can import them. Once the import finishes it cleans up the extracted
files. It integrates with **Sonarr, Radarr, Lidarr, Readarr and Whisparr**.

This package enables Unpackerr's built-in **web server on port `5656`**, which
serves a status page and Prometheus metrics — that's what the app tile opens.

## Important: match the download path

Unpackerr extracts files at the path Sonarr/Radarr report through their API —
which is the path **inside their containers**. So Unpackerr must see those files
at that same in-container path. Set:

- **Downloads path (on the host)** → the host folder your downloads live in (the
  same host folder your *arr apps mount).
- **Downloads path (inside the container)** → the in-container path your *arr
  apps use, e.g. `/downloads` or `/data/downloads`. This must match exactly, or
  extraction will fail with "file not found".

Set **PUID/PGID** to the user/group that owns those downloads so Unpackerr can
read and delete files. Match whatever your Sonarr/Radarr containers use.

## Connecting your *arr apps

Fill in the **Sonarr** and/or **Radarr** URL + API key fields (find the key
under Settings → General → API Key in each app). Use a URL the Unpackerr
container can reach — typically `http://<service>:<port>` if they share a
network, or `http://<your-tipi-ip>:<port>`. Leave a pair blank to skip that app.

Need more apps (Lidarr/Readarr/Whisparr) or multiple instances? They follow the
same `UN_<APP>_<N>_URL` / `UN_<APP>_<N>_API_KEY` pattern — see the docs.

Source: https://github.com/Unpackerr/unpackerr · Docs: https://unpackerr.zip
