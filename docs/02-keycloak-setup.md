### 2. Keycloak Installation & Configuration

## My Changes:
- used openjdk-21 instead of openjdk-17 which no longer in dnf package manager as well does not support newer version of keycloak i.e. 26.x.x
- used keycloak version 26.3.3 instead on 24.0.4
- went straight to production setup, bellow is my setup flow:
  - created a mariadb db.
  - installed keycloak
  - setup temp-admin user pass using bootstrap-admin command
  - built the keycloak using kc.sh build
  - created a systemd service
  - created 2 apache conf
    - redirect all http trafic to https
    - to server keycloak at 165.22.209.0/keycloak
  - started the systemd and httpd service
  - created a permanent admin user
  - loged into the new admin user and deleted the temp user
  - created a realm named fossee for our application setup 
  


**A. Install Java & Keycloak:**

```
# Install Java JDK 17
sudo dnf install java-17-openjdk-devel -y

# Navigate to /opt for the installation
cd /opt

# Download the latest stable Keycloak (check keycloak.org/downloads for new versions)
sudo wget [https://github.com/keycloak/keycloak/releases/download/24.0.4/keycloak-24.0.4.zip](https://github.com/keycloak/keycloak/releases/download/24.0.4/keycloak-24.0.4.zip)
sudo unzip keycloak-24.0.4.zip
sudo mv keycloak-24.0.4 keycloak

# Create a dedicated user for Keycloak
sudo groupadd keycloak
sudo useradd -r -g keycloak -d /opt/keycloak -s /sbin/nologin keycloak
sudo chown -R keycloak:keycloak /opt/keycloak
```

**B. Configure and Run Keycloak:**

For initial setup, you can run Keycloak in development mode.

```
# Start Keycloak to create an initial admin user
# The --auto-build flag is for development; we'll create a service for production
/opt/keycloak/bin/kc.sh start-dev --http-port=8080
```

Access `http://your_droplet_ip:8080`, create an admin user, and log in to the **Administration Console**.

**C. Create a Systemd Service for Production:**

Create the service file `/etc/systemd/system/keycloak.service`:

```
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

Enable and start the service:

```
sudo systemctl daemon-reload
sudo systemctl enable --now keycloak
sudo systemctl status keycloak
```
