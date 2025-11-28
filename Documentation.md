# Setup Steps

## Qbittorrent
- Check `docker compose logs qbitttorrent` for temporary login info. Should look like this:
  `qbittorrent  | ******** Information ********
  qbittorrent  | To control qBittorrent, access the WebUI at: http://localhost:8080
  qbittorrent  | The WebUI administrator username is: admin
  qbittorrent  | The WebUI administrator password was not set. A temporary password is provided for this session: xZ7u4bLD9
  qbittorrent  | You should set your own password in program preferences.`
- Log into WebUI and change password
- Also check "Bypass authentication for clients on localhost" (Tab WebUI under the login credentials)
  -> this step is crucial for gluetuns automatic portforwarding setup to work
## Gluetun
- Setup an OPENVPN config with portforwarding (f.e. proton)
- Add your OPENVPN credentials to the .env (copy the .env.example if you didn't already)
  portforwarding should work out of the box, see `docker compose logs gluetun` for details

