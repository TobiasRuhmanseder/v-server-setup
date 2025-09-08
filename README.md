V-Server Setup

Table of Content

Quick Start
Usage
1. Configure SSH Access
2. Disable Password Login
3. Create Shortcuts (Alias / Config File)
4. Install and Configure Nginx
Quick Start

For experienced users, the essential steps:

    
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
Open in browser:

    
http://123.456.789.100
Usage

1. Configure SSH Access

Generate SSH key:

    
mkdir -p ~/.ssh/myserver
ssh-keygen -t ed25519 -f ~/.ssh/myserver/id_ed25519
First login with password:

    
ssh username@123.456.789.100
Copy public key to server:

    
ssh-copy-id -i ~/.ssh/myserver/id_ed25519.pub username@123.456.789.100
Test login:

    
ssh -i ~/.ssh/myserver/id_ed25519 username@123.456.789.100
2. Disable Password Login

Edit SSH config:

    
sudo nano /etc/ssh/sshd_config
Change:

    
#PasswordAuthentication yes
To:

    
PasswordAuthentication no
Restart service:

    
sudo systemctl restart ssh.service
Verify:

    
ssh -o PubkeyAuthentication=no username@123.456.789.100
# -> Permission denied (publickey).
3. Create Shortcuts (Alias / Config File)

Alias

    
alias myserver="ssh -i ~/.ssh/myserver/id_ed25519 username@123.456.789.100"
SSH config

    
nano ~/.ssh/config
    
Host myserver
    HostName 123.456.789.100
    User username
    IdentityFile ~/.ssh/myserver/id_ed25519
Connect with:

    
ssh myserver
4. Install and Configure Nginx

Install

    
sudo apt update
sudo apt install nginx -y
Check status:

    
systemctl status nginx.service
create alternate welcome page:

    
sudo mkdir -p /var/www/alternatives
sudo nano /var/www/alternatives/alternate-index.html
Add content:

    
<h1>Hello, Nginx!</h1>
<p>Custom welcome page</p>
Add site config:

    
sudo nano /etc/nginx/sites-enabled/alternatives
    
server {
    listen 8081;
    listen [::]:8081;

    root /var/www/alternatives;
    index alternate-index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
Restart service:

    
sudo systemctl restart nginx
Open in Browser:

    
http://123.456.789.100:8081