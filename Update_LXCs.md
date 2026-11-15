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






- #### Update Docker manager:

  <details>

    <summary>Dockhand server LXC</summary>
  
    ```bash
    cd /home/dockhand && \
    docker compose pull && \
    docker compose up -d
    ```
    👉 Verify the `/home/dockhand` path and adjust it if necessary.

  </details>

  <details>

    <summary>Dockhand remote LXC (with Hawser agent)</summary>
  
    ```bash
    cd /home/hawser && \
    docker compose pull && \
    docker compose up -d
    ```
    👉 Verify the `/home/hawser` path and adjust it if necessary.

  </details>

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

## 3️⃣ In Docker manager:

- Redeploy the stack(s):
  - Enable `Pull images`
  - Enable `Force recreate` if host-side configuration (.env, server configs) changed without modifying the Docker Compose file
- Remove unused images
