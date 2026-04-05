# Update LXC

---

## 1️⃣ In Proxmox Backup Server:

- #### Mark the last backup of the LXC:

  - Set comment (e.g. 'Before update on ...')
  - (Optional) Enable prune protection

---

## 2️⃣ In LXC:

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
  <summary>Other LXC (with Portainer Agent)</summary>

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

- #### Specific updates:

  <details>
  <summary>Nextcloud AIO LXC</summary>
  
  - Start the update routine via the `Nextcloud AIO Interface`
 
  - Follow the upcoming instructions

  </details>

  <details>
  <summary>Plex Media Server LXC (created with <a href="https://community-scripts.org">Proxmox VE Scripts</a>)</summary>

  - Start the update routine
    ```bash
    update
    ```

  - Select `Update LXC`
    ```
    (*) 1  Update LXC
    ( ) 2  Install plexupdate
    ```

  - Start the update routine again
    ```bash
    update
    ```

  - Select `Install plexupdate`
    ```
    ( ) 1  Update LXC
    (*) 2  Install plexupdate
    ```

  - Answer the upcoming questions as follows:

    - Directory to install into: `/opt/plexupdate`

    - Do you want to install the latest PlexPass releases? (requires PlexPass account) [Y/n] `y`

    - Would you like to automatically install the latest release when it is downloaded? [Y/n] `y`

    - When using the auto-install option, would you like to check if the server is in use before upgrading? [Y/n] `n`

    - Would you like to set up automatic daily updates for Plex? [Y/n] `n`

    - Configuration complete. Would you like to run plexupdate with these settings now? [Y/n] `y`

  </details>

---

## 3️⃣ In Portainer:

- Update and redeploy stack(s)
- Remove unused images
