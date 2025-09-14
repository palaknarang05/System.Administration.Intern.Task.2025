# 02 – Keycloak Setup

This document explains how to install and configure Keycloak on your DigitalOcean server.


## A. Install Java
```bash
# Install OpenJDK 17
sudo dnf install java-17-openjdk-devel -y

# Verify Java installation
java -version
```
## B. Download & Install Keycloak
```bash
cd /opt
sudo wget https://github.com/keycloak/keycloak/releases/download/24.0.4/keycloak-24.0.4.zip
sudo unzip keycloak-24.0.4.zip
sudo mv keycloak-24.0.4 keycloak
```
## C. Create Keycloak User & Permissions
```bash
sudo groupadd keycloak
sudo useradd -r -g keycloak -d /opt/keycloak -s /sbin/nologin keycloak
sudo chown -R keycloak:keycloak /opt/keycloak
```
## D. Start Keycloak (Development Mode)
```bash
/opt/keycloak/bin/kc.sh start-dev --http-port=8080
```
## E. Create Systemd Service for Production

Create the file `/etc/systemd/system/keycloak.service`:

```ini
[Unit]
Description=Keycloak Authorization Server
After=network.target

[Service]
Type=idle
User=keycloak
Group=keycloak
ExecStart=/opt/keycloak/bin/kc.sh start --optimized --http-port=8080
LimitNOFILE=102400
LimitNPROC=102400
TimeoutStartSec=600
Restart=on-failure
RestartSec=30

[Install]
WantedBy=multi-user.target
```
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now keycloak
sudo systemctl status keycloak
```
