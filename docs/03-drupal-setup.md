### 1. Drupal 11 Setup & SSO

## My Changes:
- the drupal/keycloak module mentioned in task is only supported till version 10 so i used miniorange oauth and openid module

  
**A. Create Database:**

```
-- Log in to MariaDB
sudo mysql -u root -p

-- Create database and user
CREATE DATABASE drupaldb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'drupaluser'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON drupaldb.* TO 'drupaluser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**B. Install Drupal & Configure Apache:**

```
# Install Drupal using Composer for better dependency management
cd /var/www/
sudo dnf install composer -y
sudo composer create-project drupal/recommended-project drupal
sudo chown -R apache:apache /var/www/drupal
sudo chmod -R 755 /var/www/drupal/web
```

Create an Apache virtual host at `/etc/httpd/conf.d/drupal.conf`:

```
<VirtualHost *:80>
    ServerName your_drupal_domain.com
    DocumentRoot /var/www/drupal/web

    <Directory /var/www/drupal/web>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog /var/log/httpd/drupal_error.log
    CustomLog /var/log/httpd/drupal_access.log combined
</VirtualHost>
```

Restart Apache: `sudo systemctl restart httpd`. Then, navigate `http://your_drupal_domain.com` to complete the web installation.

**C. Integrate Keycloak SSO:**

1. Install Module: In your Drupal directory (/var/www/drupal), run:
    
    sudo composer require drupal/keycloak
    
2. **Enable Module:** In the Drupal admin UI, go to **Extend** and enable the **Keycloak** module.
    
3. **Create Keycloak Client:**
    
    - In the Keycloak Admin Console, go to **Clients > Create client**.
        
    - **Client ID:** `drupal`
        
    - **Client authentication:** ON
        
    - **Valid redirect URIs:** `http://your_drupal_domain.com/keycloak/oauth2/callback`
        
    - **Web origins:** `http://your_drupal_domain.com`
        
    - Save, then copy the **Client Secret** from the **Credentials** tab.
        
4. **Configure Drupal Module:**
    
    - In Drupal, go to **Configuration > Web services > Keycloak**.
        
    - Enter your Keycloak URL (`http://your_droplet_ip:8080`), Realm (`master`), Client ID, and Client Secret.
        
