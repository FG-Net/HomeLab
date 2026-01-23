# Updating LXC Containers

---

## 1️⃣ Proxmox Backup Server

- #### Mark the last backup of the LXC container:

  - Set comment to 'Before update'
  - (Optional) Enable prune protection

---

## 2️⃣ In LXC Container

- #### If Nextcloud AIO:

  - Update via ```Nextcloud AIO Interface``` first

- #### Update the system:

  ```bash
  apt update && apt upgrade -y && apt autoremove -y && apt autoclean -y
  ```

- #### Update Portainer:

  <details>
  <summary>Portainer LXC (Portainer Server)</summary>

   ```bash
  docker stop portainer && \
  sleep 3 && \
  docker rm portainer && \
  sleep 3 && \
  docker pull portainer/portainer-ce:lts && \
  sleep 3 && \
  docker run -d -p 8000:8000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:lts
  ```

  </details>

  <details>
  <summary>Other LXC (Portainer Agent)</summary>

  ```bash
  docker stop portainer_agent && \
  sleep 3 && \
  docker rm portainer_agent && \
  sleep 3 && \
  docker pull portainer/agent:lts && \
  sleep 3 && \
  docker run -d -p 9001:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent:lts
  ```

  </details>

  <sub>👉 https://docs.portainer.io/start/upgrade/docker</sub>

---

## 3️⃣ In Portainer

- Update and redeploy stack(s)
- Remove unused images
