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
