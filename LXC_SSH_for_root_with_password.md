# SSH Access for Root in a Proxmox LXC

:information_source: Debian/Ubuntu

:warning: Root login via password ➔ **insecure**

## 1. Install SSH (if not already installed)

```bash
apt update
apt install openssh-server -y
systemctl enable ssh
systemctl start ssh
```

## 2. Configure SSH

```bash
nano /etc/ssh/sshd_config
```

Set:  
```
PermitRootLogin yes
PasswordAuthentication yes
```
(remove `#` if commented out):

## 3. Restart SSH

```bash
systemctl restart ssh
```

## 4. Test connection  

From client (e.g. Windows CMD/PowerShell):  

```bash
ssh root@<lxc-ip-address>
```
