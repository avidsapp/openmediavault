# openmediavault

Adapted from [this YouTube playlist](https://www.youtube.com/watch?v=TYewyAK6GmQ)

## Features
- Raspberry Pi OS Lite (arm64)
- OpenMediaVault - port 90
- OpenVPN - WIP
- Portainer - port 9000
- Cloudflare DDNS
- Nginx Proxy Manager - port 91
- Pi-Hole DNS AdBlocker - WIP
- Duplicati - port 8200
- Nextcloud - WIP
- RPi Monitor - port 8888

## Install OS

Set up the Raspberry Pi following the guide [here](https://github.com/avidsapp/rpi-docker-server/blob/master/RASPIOS.md). Stop before `Configuration`.

## Install openmediavault

```
sudo apt update && sudo apt upgrade -y
wget -O - https://raw.githubusercontent.com/OpenMediaVault-Plugin-Developers/installScript/master/install | sudo bash
```

## Set up openmediavault
Visit `http://[raspberrypiipaddress]` and follow instructions [here](https://pimylifeup.com/raspberry-pi-openmediavault/)

Settings for Mac/Linux/Windows cross-compatibility:
- System
    - General Settings
        - Enable SSL (after creating a cert)
        - Change Web Administrator Password
    - Date & Time - update
    - Network
        - Hostname
    - Certificates > SSL - create a cert
    - Plugins
        - fail2ban
    - OMV-Extras > Docker
        - Docker > Install
        - Portainer > Install
- Storage
    - Disks
        - Wipe disks, if necessary
    - File Systems
        - Create/mount external drives
- Access Rights Management
    - User
        - Add user and add to all groups
        - Be sure to add an email and password in order to mount the shared folders on client machines
        - Privileges: add new user to Read/Write for all shared folders
    - Shared Folders
        - Add desired folders to share
        - Give all users to access shared folders Read/Write privileges
        - ACL - give all users to access shared folders Read/Write privileges
            - Owner: root - Read/Write/Execute
            - Group: users - Read/Write/Execute
            - Others: Read/Write/Execute
- Services
    - Fail2ban - enable and add shares
    - NFS - enable (for Linux)
    - Rsync - add
        - 0 7 * * * = every day backup
    - SMB/CIFS - enable and add shares
        - Public: No
        - Read only: No
        - Browseable: Yes
    - SSH - enable

## Make openmediavault accessible outside of LAN
If your server is behind a LAN, you'll most likely need a dynamic DNS service and port forwarding at your router.

### Dynamic DNS
Options:
1. On your router
1. Google/DuckDNS/No-IP
1. DuckDNS/Cloudflare (provided) DDNS on the server

### VPN
Options:
1. On your router
1. OMV OpenVPN plugin
1. [OpenVPN Raspberry Pi server](https://github.com/avidsapp/docker-openvpn-pihole)

## Extras
- [Duplicati backups](https://www.youtube.com/watch?v=-NyzdAYMarw&list=PLhMI0SExGwfAU-UMeKxd1Lu5_a60AlA9N&index=4)
- RPi Monitor:
    ```
    docker run --device=/dev/vchiq \
    --device=/dev/vcsm-cma \
    --volume=/opt/vc:/opt/vc \
    --volume=/boot:/boot \
    --volume=/sys:/dockerhost/sys:ro \
    --volume=/etc:/dockerhost/etc:ro \
    --volume=/proc:/dockerhost/proc:ro \
    --volume=/usr/lib:/dockerhost/usr/lib:ro \
    -p=8888:8888 \
    --name="rpi-monitor" \
    -d \
    michaelmiklis/rpi-monitor:latest
    ```
