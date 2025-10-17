# Mounting a NAS Share (CIFS/SMB) in Proxmox and Using It in an <u>unprivileged</u> LXC Container

Mount a NAS share on the Proxmox host via CIFS/SMB, pass it through to an unprivileged LXC container, and use it there with correct user and group permissions.

---

## 1️⃣ Prepare the NAS

### 🔹 Create user and group

- #### Create group:
   ```
   media-users
   ```

- #### Create user:
   ```
   media-user
   ```
   - and assign it to the `media-users` group

- #### Note UID/GID:
   Example:
   ```
   media-user : uid=1234
   media-users: gid=4321
   ```

### 🔹 Create NAS folder share

- #### Create NAS folder:
   ```
   Media
   ```

- #### Create share:
   - Share name: `Media`  
   - Enable SMB/CIFS  
   - Allow read/write for the `media-users` group  
   - Disable guest access

## 2️⃣ Prepare the Proxmox Host

### 🔹 Create mount point
```bash
mkdir -p /mnt/shares/nas/media
```

### 🔹 Create credentials file
```bash
nano /root/.media-smb-credentials
```

- #### Content:
   ```
   username=media-user
   password=<PASSWORD>
   domain=WORKGROUP
   ```
   - Replace `<PASSWORD>` with the password of `media-user`

- #### Protect credentials file
   ```bash
   chmod 0600 /root/.media-smb-credentials
   ```
   - `0600` → rw- owner, --- group/others

### 🔹 Create mount
```bash
nano /etc/fstab
```

- #### Add following line:
   ```
   //<NAS_IP_ADDRESS>/Media /mnt/shares/nas/media cifs _netdev,x-systemd.automount,noatime,uid=<NAS_USER_UID_MAPPING>,gid=<NAS_GROUP_GID_MAPPING>,dir_mode=0770,file_mode=0660,credentials=/root/.media-smb-credentials 0 0
   ```
   - Replace `<NAS_IP_ADDRESS>` with the IP address of the NAS
   - Replace `<NAS_USER_UID_MAPPING>` with the value noted before + 100000 (for unprivileged LXC)
   - Replace `<NAS_USER_GID_MAPPING>` with the value noted before + 100000 (for unprivileged LXC)

   **Explanation:**
   | Option | Meaning |
   |--------|----------|
   | `uid, gid` | Ownership of directories and files, regardless of the user who created or modified them |
   | `dir_mode, file_mode` | Permissions of directories and files <br> `0770` → rwx owner/group, --- others <br> `0660` → rw- owner/group, --- others |
   | `credentials` | Path to login file |
   | `_netdev` | Wait for network availability at boot |
   | `x-systemd.automount` | Auto-mount on access (saves boot time) |
   | `noatime` | Don't update access time on each read (saves I/O, useful for media servers) |

   **Example:**
   ```
   # <NAS_IP_ADDRESS>      : 192.168.10.123
   # <NAS_USER_UID_MAPPING>: 101234 (1234 + 100000)
   # <NAS_USER_GID_MAPPING>: 104321 (4321 + 100000)

   //192.168.10.123/Media /mnt/shares/nas/media cifs _netdev,x-systemd.automount,noatime,uid=101234,gid=104321,dir_mode=0770,file_mode=0660,credentials=/root/.media-smb-credentials 0 0
   ```

### 🔹 Enable mount
```bash
mount -a
```
```bash
systemctl daemon-reload
```

---

## 3️⃣ Prepare the LXC Container (unprivileged)

### 🔹 On Proxmox Host:

   - #### Stop container

   - #### Option 1: Add mount via `.conf` file
      ```bash
      nano /etc/pve/lxc/<CT_ID>.conf
      ```
      - Replace `<CT_ID>` with the ID of the container
            
      Add the following line before the `net0:` entry or after an existing `mpX:` entry:
      ```
      mp<NR>: /mnt/shares/nas/media,mp=/mnt/media
      ```
      - Replace `<NR>` with 0, 1, 2, etc., depending on existing `mpX:` entries

  - #### Option 2: Add mount via `pct`
      ```bash
      pct set <CT_ID> -mp<NR> /mnt/shares/nas/media,mp=/mnt/media
      ```
      - Replace `<CT_ID>` with the ID of the container
      - Replace `<NR>` with 0, 1, 2, etc., depending on existing mount points (check the `Resources` page of the LXC container)

   - #### Start container

### 🔹 On LXC Container:

  >  The mount was created with a specified UID and GID, which determine the ownership of directories and files, regardless of the user who created or modified them.
  >
  >  Non-root users must be added to the group to access the directories and files owned under that UID and GID.
  >
  >  root doesn’t need to be added to the group, since it inherently has full access to all directories and files.

   - #### Create group
      ```bash
      groupadd -g <NAS_USER_GID> media-users
      ```
      - Replace `<NAS_USER_GID>` with the value noted before

   - #### Create user
      ```bash
      useradd -u <NAS_USER_UID> -g media-users -m media-user
      ```
      - Replace `<NAS_USER_UID>` with the value noted before

   - #### Add existing user to group (e.g. **plex**)
      ```bash
      usermod -aG media-users <USER_NAME>
      ```
      - Replace `<USER_NAME>` with the name of an existing user

