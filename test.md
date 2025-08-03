# <img src="https://raw.githubusercontent.com/filebrowser/logo/master/banner.png" alt="File Browser" width="500">

## Installation (with Docker Compose)

### Preparations

Update the system and install Docker and Docker Compose

### Create config directory

```
cd </path/to/apps/directory>
```

```
mkdir -p filebrowser/config && cd $_
```

### Create database file

Create an empty database file manually as the automatic creation (when processing the docker-compose.yml file) would create a folder instead of a file.

```
touch filebrowser.db
```

### Create config file

Create a config file with the specified content.

```
nano .filebrowser.json
```

```
{
  "port": 80,
  "baseURL": "",
  "address": "",
  "log": "stdout",
  "database": "/database.db",
  "root": "/srv"
}
```

### (Optional) Provide custom logo

:bell: NOTE:
The custom logo must be a SVG file and have the name logo.svg.

```
mkdir -p branding/img
```

Copy your custom logo to

```
</path/to/apps/directory>/filebrowser/config/branding/img
```

### docker-compose.yml

```
services:
  filebrowser:
    image: filebrowser/filebrowser
    restart: unless-stopped
    ports:
      - 8080:80
    environment:
      - PUID=0 # 'root' user ID in a Proxmox container
      - PGID=0 # 'root' user group ID in a Proxmox container
    volumes:
      # Configuration
      - </path/to/apps/directory>/filebrowser/config/filebrowser.db:/database.db
      - </path/to/apps/directory>/filebrowser/config/.filebrowser.json:/.filebrowser.json
      # Custom branding
      #- </path/to/apps/directory>/filebrowser/config/branding:/branding
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
