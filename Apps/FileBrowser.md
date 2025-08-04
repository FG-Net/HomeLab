<a href="https://github.com/filebrowser/filebrowser" target="_blank" rel="noopener noreferrer">
  <img src="https://raw.githubusercontent.com/filebrowser/logo/master/banner.png" alt="File Browser" width="500">
</a>

[GitHub](https://github.com/filebrowser/filebrowser) | [Documentation](https://filebrowser.org)

## Installation (with Docker Compose)

### Preparations

Update the system and install Docker and Docker Compose.

### Create folder structure

:bell: Replace `</path/to>` with your preferred path.

```bash
mkdir -p </path/to>/filebrowser/{database,config,config/branding/img/icons}
```

Folder structure:
```text
</path/to>
└── filebrowser
    ├── config
    |   └── branding
    |       └── img
    |           └── icons
    └── database
```
---
### docker-compose.yaml

:bell: Replace `</path/to>` with your preferred path.

:bell: Uncomment the `Custom branding` volume mapping line if you want to use your own logo.

```yaml
services:
  filebrowser:
    image: filebrowser/filebrowser:s6
    container_name: filebrowser
    restart: unless-stopped
    ports:
      - 8080:80
    environment:
      - PUID=0 # 'root' user ID in a Proxmox container
      - PGID=0 # 'root' user group ID in a Proxmox container
    volumes:
      # Configuration
      - </path/to/>/filebrowser/database:/database
      - </path/to/>/filebrowser/config:/config
      # [Optional] Custom branding
      #- </path/to/>/filebrowser/config/branding:/branding
      # Data
      - /home:/srv # Root directory. Change to refer to your preferred location.
      #- /dev/sbc/documents:/srv/MyDocuments # Additional directory
      #- /dev/sdc1:/srv/MyDrive # Additional directory refering to a disk
```
---

### First login

Check the Docker container logs to find the default username and password.

```bash
docker logs filebrowser
```
---





### [Optional] Provide custom logo

> :bell: The custom logo must be a SVG file and have the name `logo.svg`.

```bash
mkdir -p branding/img
```

```
Folder structure
----------------
/mnt
 └── nas-media
      ├── Bibliothek
      │    ├── Filme
      │    └── Serien
      └── Downloads
           └── incomplete
/srv
 └── configs
      ├── overseerr
      ├── radarr
      ├── sonarr
      ├── prowlarr
      └── sabnzbd
```



## After installation

### Login

- Username: admin
- Password: admin

### \[Optional\] Enable custom logo

Set Settings ⇒ Global Settings ⇒ Branding ⇒ Branding directory path to /branding and apply with <kbd>UPDATE</kbd>

:bell: NOTE:
To see the custom logo you may have to clear the browser cache for the File Browser page.
For example in Chrome:
 • Open the Developer Tools by pressing <kbd>F12</kbd>.
 • Right click on the page refresh button <kbd>⟳</kbd> and select Empty Cache and Hard Reload
 • Close the Developer Tools by pressing <kbd>F12</kbd>.
