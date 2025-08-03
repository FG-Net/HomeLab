<a href="https://github.com/filebrowser/filebrowser" target="_blank" rel="noopener noreferrer">
  <img src="https://raw.githubusercontent.com/filebrowser/logo/master/banner.png" alt="File Browser" width="500">
</a>

[GitHub](https://github.com/filebrowser/filebrowser) | [Documentation](https://filebrowser.org)

## Installation (with Docker Compose)

### Preparations

Update the system and install Docker and Docker Compose

### Create config directory

```bash
cd </path/to/apps/folder>
```

```bash
mkdir -p filebrowser/config && cd $_
```

### Create empty database file

```bash
touch filebrowser.db
```

> :bell: Create the database file manually bacause the automatic creation (when processing the docker-compose.yaml file) may create a folder instead of a file.

### Create config file

```bash
nano settings.json
```

```json
{
  "port": 80,
  "baseURL": "",
  "address": "",
  "log": "stdout",
  "database": "/database/filebrowser.db",
  "root": "/srv"
}
```

### (Optional) Provide custom logo

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

### docker-compose.yaml

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
      - </path/to/apps/folder>/filebrowser/config:/database
      - </path/to/apps/folder>/filebrowser/config:/config
      # Custom branding
      #- </path/to/apps/folder>/filebrowser/config/branding:/branding
      # Data
      - /home:/srv # Root directory. Change to refer to your preferred location.
      #- /dev/sbc/documents:/srv/MyDocuments # Additional directory
      #- /dev/sdc1:/srv/MyDrive # Additional directory refering to a disk
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
