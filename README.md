# Homelab Stack for managing and monitoring dockerized services
This repo consists of several services, and corresponding tools to monitor
and manage said services. They are all divided into subfolders with docker-compose files.

If you are only interested in the arr-stack, jump to the Mediastack Section. U can use it in isolation
without worrying about the rest.

This README.md just aims to provide the steps for a basic setup. I don't explain what the services are for
and how to use them in detail.

## todo's
- evaluate jellyfin/jellyfin vs linuxserver/jellyfin

## Networking (folder: networ)

### Traefik Reverse Proxy
Traefik is used as a reverse proxy. This allows setting a domain (or other rules) to directly
redirect to a service. Because those can be registered via docker labels, you technically don't even
need to expose your ports on the host. For ease-of-use and debug purposes, i still have the ports
exposed by default.
All domains are set in the .env files of the respective subfolders.
For the default local config, add the following string to your /etc/hosts (pointing to 127.0.0.1):
`pihole.local media.local qbit.local radarr.local prowlarr.local bazarr.local sonarr.local`

For more info start here: https://doc.traefik.io/traefik/getting-started/docker/

### Pihole DNS
Pihole is a DNS based ad-blocker. Add it as a DNS in your Router and it will filter out / drop
DNS Requests that are associated with Ads.
More info: https://pi-hole.net/

## Mediastack (folder: media)
This is a fully dockerized arr-stack, used to download and managa various media,
fully setup to use a vpn with portforwarding to download torrents

### Env variables
- Copy the env example `cp .env.example .env` and set variables

### Qbittorrent
- Check `docker compose logs qbitttorrent` for temporary login info. Should look like this:
  `qbittorrent  | ******** Information ********
  qbittorrent  | To control qBittorrent, access the WebUI at: http://localhost:8080
  qbittorrent  | The WebUI administrator username is: admin
  qbittorrent  | The WebUI administrator password was not set. A temporary password is provided for this session: xZ7u4bLD9
  qbittorrent  | You should set your own password in program preferences.`
- Log into WebUI and change password
- Also check "Bypass authentication for clients on localhost" (Tab WebUI under the login credentials)
  -> this step is crucial for gluetuns automatic portforwarding setup to work
### Gluetun
- Setup an OPENVPN config with portforwarding (f.e. proton)
- Add your OPENVPN credentials to the .env (copy the .env.example if you didn't already)
  portforwarding should work out of the box, see `docker compose logs gluetun` for details

### Prowlarr
- Open Prowlarr (http://your_host:9696) and do the initial configuration
- Click add Indexer, add your Indexer (duh)
- Do the radarr and sonarr steps before going further here
- In radarr or sonarr, go to Settings->General and copy the API Key
- Back in prowlarr, go to Settings->Apps and add radarr/sonarr, paste in the API Key, replace localhost with the docker container names (radarr,prowlarr,sonarr)

### Radarr / Sonarr
Tese to are functionaly the same, just for different purposes (radarr->movies, sonarr->series)
- Open Radarr(http://your_host:7878) or Sonarr(http://your_host:8989) and do the initial configuration
- Go to Settings->DownloadClients, add qBittorrent. Important: if you use gluetun, write gluetun in the hostname, else use qbittorrent
- Go to Settings->MediaManagement, add the root folders (/movies and /series) respectively

### Jellyfin
- The Setup starts automatically once you open the WebUI, add the movies/series folder and you're good to go

## Monitoring (folder: monitoring)
@todo document monitoring setup

## Collaboration / Messaging (folder: collaboration)
Mattermost is used, to get automated alerts from the monitoring.
It can also be used for all things communication in the team (hence collaboration)

### Setup
Before doing `docker compose up -d`, be sure to create the following folders / permissions:

`sudo mkdir -p config data logs
sudo touch config/config.json
sudo chown -R 2000:2000 config data logs`

