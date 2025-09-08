# V-Server Setup

## Table of Content

- [Quick Start](#quick-start)
- [Usage](#usage)
  - [1. Configure SSH Access](#1-configure-ssh-access)
  - [2. Disable Password Login](#2-disable-password-login)
  - [3. Create Shortcuts (Alias / Config File)](#3-create-shortcuts-alias--config-file)
  - [4. Install and Configure Nginx](#4-install-and-configure-nginx)


## Quick Start

For experienced users, the essential steps:

```bash
# 1. Generate SSH key
ssh-keygen -t ed25519 -f ~/.ssh/myserver/id_ed25519

# 2. Copy public key to server
ssh-copy-id -i ~/.ssh/myserver/id_ed25519.pub username@123.456.789.100

# 3. Disable password login
sudo nano /etc/ssh/sshd_config
# -> set PasswordAuthentication no
sudo systemctl restart ssh.service

# 4. Install Nginx
sudo apt update
sudo apt install nginx -y
```

Open in browser:
```bash
http://123.456.789.100
```


## Usage

### 1. Configure SSH Access

**Generate SSH key:**
```bash
mkdir -p ~/.ssh/myserver
ssh-keygen -t ed25519 -f ~/.ssh/myserver/id_ed25519
```

**First login with password:**
```bash
ssh username@123.456.789.100
```

**Copy public key to server:**
```bash
ssh-copy-id -i ~/.ssh/myserver/id_ed25519.pub username@123.456.789.100
```

**Test login:**
```bash
ssh -i ~/.ssh/myserver/id_ed25519 username@123.456.789.100
```


### 2. Disable Password Login

**Edit SSH config:**
```bash
sudo nano /etc/ssh/sshd_config
```

**Change:**
```bash
#PasswordAuthentication yes
```

**To:**
```bash
PasswordAuthentication no
```

**Restart service:**
```bash
sudo systemctl restart ssh.service
```

**Verify:**
```bash
ssh -o PubkeyAuthentication=no username@123.456.789.100
# -> Permission denied (publickey).
```

### 3. Create Shortcuts (Alias / Config File)

**Alias**
```bash
alias myserver="ssh -i ~/.ssh/myserver/id_ed25519 username@123.456.789.100"
```

**SSH config**
```bash
nano ~/.ssh/config
```

```bash
Host myserver
    HostName 123.456.789.100
    User username
    IdentityFile ~/.ssh/myserver/id_ed25519
```

Connect with:
```bash
ssh myserver
```

### 4. Install and Configure Nginx

**Install**
```bash
sudo apt update
sudo apt install nginx -y
```

**Check status:**
```bash
systemctl status nginx.service
```

**create alternate welcome page:**
```bash
sudo mkdir -p /var/www/alternatives
sudo nano /var/www/alternatives/alternate-index.html
```

Add content:
```html
<h1>Hello, Nginx!</h1>
<p>Custom welcome page</p>
```

**Add site config:**
```bash
sudo nano /etc/nginx/sites-enabled/alternatives
```

```nginx
server {
    listen 8081;
    listen [::]:8081;

    root /var/www/alternatives;
    index alternate-index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

**Restart service:**
```bash
sudo systemctl restart nginx
```

Open in Browser:
```url
http://123.456.789.100:8081
```



