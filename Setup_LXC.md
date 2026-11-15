# Setup New LXC

---

## 1️⃣ Preparations

#### Update system

  ```bash
  apt update && apt upgrade -y && apt autoremove -y && apt autoclean -y
  ```

#### Setup locale

- ##### Check locale

  ```bash
  locale
  ```

- ##### Set locale

  ```bash
  dpkg-reconfigure locales
  ```

  ➜ Locales to be generated: ```en_US.UTF-8 UTF-8```

  ➜ Default locale: ```en_US.UTF-8```

#### Setup time zone

- ##### Check time zone

  ```bash
  timedatectl
  ```

- ##### Set time zone

  ```bash
  dpkg-reconfigure tzdata
  ```

  ➜ Geographic area: ```Europe```

  ➜ Time zone: ```Berlin```

#### Reboot

  ```bash
  reboot
  ```

---

## 2️⃣ Docker

#### Install curl

```bash
apt install curl -y
```

#### Install Docker

```bash
curl -sSL https://get.docker.com | sh
```

#### Install Docker Compose

```bash
curl -SL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

#### Set Docker Commpose permissions:

```bash
chmod +x /usr/local/bin/docker-compose
```

#### Verify Docker and Docker Compose

```bash
docker --version && docker-compose --version
```

---

## 3️⃣ Docker manager

<details>
  <summary>Dockhand</summary>

  #### Create and navigate into the Dockhand directory structure:

  ```bash
  mkdir -p /home/dockhand/data && cd $_/..
  ```
  
  #### Create Docker Compose file
  
  ```bash
  nano compose.yaml
  ```

  File content:
  ```yaml
  services:
    dockhand:
      image: fnsys/dockhand:latest
      container_name: dockhand
      restart: unless-stopped
      ports:
        - "3000:3000"
      volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        - /home/dockhand/data:/app/data
  ```
  
  #### Start Dockhand
  
  ```bash
  docker compose up -d
  ```

</details>

<details>
  <summary>Hawser (Dockhand agent)</summary>

  #### Create and navigate into the Hawser directory structure:

  ```bash
  mkdir -p /home/hawser/stacks && cd $_/..
  ```
  
  #### Create Docker Compose file
  
  ```bash
  nano compose.yaml
  ```
  
  File content:
  ```yaml
  services:
    hawser:
      image: ghcr.io/finsys/hawser:latest
      container_name: hawser
      restart: unless-stopped
      ports:
        - 2376:2376
      environment:
        - TOKEN=${HAWSER_TOKEN}
      volumes:
        - /var/run/docker.sock:/var/run/docker.sock
        - /home/hawser/stacks:/data/stacks
  ```
  
  #### Create Docker enviroment file
  
  ```bash
  nano .env
  ```
  
  File content:
  ```env
  HAWSER_TOKEN=my-secret-token
  ```
  
  #### Start Hawser
  
  ```bash
  docker compose up -d
  ```

</details>
