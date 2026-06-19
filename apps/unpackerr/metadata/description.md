# Unpackerr

Unpackerr is a lightweight daemon that runs on your download host and
**automatically extracts completed archives** (rar, zip, etc.) so your media
manager can import them. Once the import finishes it cleans up the extracted
files. It integrates with **Sonarr, Radarr, Lidarr, Readarr and Whisparr**.

This package enables Unpackerr's built-in **web server on port `5656`**, which
serves a status page and Prometheus metrics — that's what the app tile opens.

## Important: share the downloads folder

Unpackerr must see your downloads at the **same path** your *arr apps do. During
install set **Downloads path (on the host)** to that shared host folder; it is
mounted into the container at `/downloads`.

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
