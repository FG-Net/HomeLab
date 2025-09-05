# SSH Access for root in a Proxmox LXC

:information_source: For Debian/Ubuntu

:warning: **Not safe:** root login via password

## Install SSH (if not already installed)

```bash
apt update
apt install openssh-server -y
systemctl enable ssh
systemctl start ssh
```

## Configure SSH

```bash
nano /etc/ssh/sshd_config
```

Set:  
```
PermitRootLogin yes
PasswordAuthentication yes
```
(remove `#` if commented out):

## Restart SSH

```bash
systemctl restart ssh
```

## Test connection  

From client (e.g. Windows CMD/PowerShell):  

```bash
ssh root@<lxc-ip-address>
```
