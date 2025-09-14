# 01 – Server Setup

## A. Create Digital Ocean Droplet
1. Log in to your Digital Ocean account.
2. Click **Create > Droplets**.
3. Choose **Rocky Linux 10** as the operating system.
4. Select a plan with **at least 2GB RAM**.
5. Enable **IPv4** (and optionally IPv6).
6. Add your **SSH key** for secure login.
7. Set a hostname and click **Create Droplet**.

---

## B. Connect to Droplet
```bash
ssh root@your_droplet_ip
```
## C. Create a New User
```bash
# Add a new user
adduser your_username

# Set a password
passwd your_username

# Give sudo privileges
usermod -aG wheel your_username

# Copy SSH key for passwordless login
rsync --archive --chown=your_username:your_username ~/.ssh /home/your_username
```
## D. Disable Root SSH Login
```bash
# Edit SSH configuration
sudo nano /etc/ssh/sshd_config
# Change: PermitRootLogin yes → PermitRootLogin no

# Apply changes
sudo systemctl restart sshd
```
## E. Configure Firewall
```bash
# Enable and start firewalld
sudo systemctl enable --now firewalld

# Allow essential services permanently
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-port=8080/tcp  # Keycloak
sudo firewall-cmd --permanent --add-service=ssh

# Reload firewall rules
sudo firewall-cmd --reload
```
## F. Update System & Install Core Components
```bash
# Update all packages
sudo dnf update -y

# Install EPEL and Remi repositories
sudo dnf install epel-release -y
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-10.rpm -y
sudo dnf module enable php:remi-8.3 -y

# Install Apache, PHP, MariaDB, Python, and other tools
sudo dnf install httpd php php-cli php-mysqlnd php-gd php-xml php-mbstring php-json php-fpm mariadb-server python3 python3-pip unzip wget -y

# Enable and start core services
sudo systemctl enable --now httpd
sudo systemctl enable --now php-fpm
sudo systemctl enable --now mariadb

# Secure MariaDB installation
sudo mysql_secure_installation
```

