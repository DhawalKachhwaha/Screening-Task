### 1. Digital Ocean Droplet & Initial Setup

**A. Created a Droplet by following these steps:**

1. **Log in** to your Digital Ocean control panel.
    
2. Click **Create > Droplets**.
    
3. **Operating System:** Choose **Rocky Linux 10**.
    
4. **Plan:** Select a plan that meets your needs (e.g., Basic Shared CPU with 2GB+ RAM).
    
5. **IP:** IPv6 should be enabled along with IPv4.
    
6. **Datacenter:** Choose a region close to your users.
    
7. **Authentication:** Add your **SSH key**. This is more secure than using a password.
    
8. **Finalize:** Set a hostname and click **Create Droplet**.
    

**B. Initial Server Hardening:**

Connect to your new Droplet as the root user.

```
ssh root@165.22.209.0
```

Create a new user and grant administrative privileges.

```
# Create the user and set a strong password
adduser dhawal
passwd dhawal

# Add the user to the 'wheel' group to grant sudo access
usermod -aG wheel dhawal
```

Copy your SSH key to the new user to allow direct SSH access.

```
# Copy SSH key for passwordless login
rsync --archive --chown=dhawal:dhawal ~/.ssh /home/dhawal
```

Disable root SSH login for better security. Edit `/etc/ssh/sshd_config` and change `PermitRootLogin yes` to `PermitRootLogin no`.

```
# Apply the change
sudo systemctl restart sshd
```

Log out and log back in as your new user: `ssh dhawal@165.22.209.0`.

**C. Configure the Firewall:**

```
# Enable and start the firewall
sudo systemctl enable --now firewalld

# Allow essential services permanently
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-port=8080/tcp # For Keycloak
sudo firewall-cmd --permanent --add-service=ssh

# Apply the new rules
sudo firewall-cmd --reload
```

**D. Update System & Install Core Components:**

```
# Update all system packages
sudo dnf update -y

# Install EPEL and Remi repositories for up-to-date packages
sudo dnf install epel-release -y
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-10.rpm
sudo dnf module enable php:remi-8.3 -y

# Install Apache, PHP, MariaDB, Python, and other tools
sudo dnf install httpd php php-cli php-mysqlnd php-gd php-xml php-mbstring php-json php-fpm mariadb-server python3 python3-pip unzip wget -y

# Enable and start core services
sudo systemctl enable --now httpd
sudo systemctl enable --now php-fpm
sudo systemctl enable --now mariadb

# Secure your database installation
sudo mysql_secure_installation
```
