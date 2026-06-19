# Unpackerr

Unpackerr is a lightweight daemon that **automatically extracts completed
archives** (rar, zip, etc.) so your media manager can import them, then cleans
up the extracted files afterward. It integrates with **Sonarr, Radarr, Lidarr,
Readarr and Whisparr**.

This package enables Unpackerr's built-in **web server on port `5656`**, which
serves a status page and Prometheus metrics — that's what the app tile opens.

## Media folder (no setup needed)

Unpackerr mounts the same `${ROOT_FOLDER_HOST}/media` folder as the official
Runtipi *arr apps, exposed inside the container at `/media`. Because Sonarr and
Radarr use the identical `/media` path, the download locations they report line
up automatically — there's nothing to configure. (Set your Runtipi **storage /
root folder** in Settings if you haven't already.)

It runs as user `1000:1000` to match the files the official *arr apps write.

## Connecting your *arr apps

Unpackerr has no setup UI, so it reads its connections from the install form.
Fill in the **Sonarr** and/or **Radarr** URL + API key (find the key under
Settings → General → API Key in each app). On the Tipi network you can reach
another app by its id, e.g. `http://sonarr:8989` and `http://radarr:7878`. Leave
a pair blank to skip that app.

Need more apps (Lidarr/Readarr/Whisparr) or multiple instances? They follow the
same `UN_<APP>_<N>_URL` / `UN_<APP>_<N>_API_KEY` pattern — see the docs.

Source: https://github.com/Unpackerr/unpackerr · Docs: https://unpackerr.zip
