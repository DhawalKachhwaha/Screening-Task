## Page 3: PHP Application 

## No major changes from my side 

### 1. Generic PHP Project Setup

**A. Deploy Application:**

Place your PHP application files in a dedicated directory.

```
sudo mkdir /var/www/php_app
# Copy your PHP application files to this directory
sudo chown -R apache:apache /var/www/php_app
sudo chmod -R 755 /var/www/php_app
```

**B. Configure Apache:**

Create `/etc/httpd/conf.d/php_app.conf`:

```
<VirtualHost *:80>
    ServerName your_php_app_domain.com
    DocumentRoot /var/www/php_app

    <Directory /var/www/php_app>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Restart Apache: `sudo systemctl restart httpd`.

### 2. PHP Keycloak SSO Integration

We'll use a standard OIDC client library for PHP.

1. **Install Library:**
    
    ```
    cd /var/www/php_app
    sudo composer require jumbojett/openid-connect-php
    ```
    
2. **Create Keycloak Client:**
    
    - **Client ID:** `php-app`
        
    - **Valid redirect URIs:** `http://your_php_app_domain.com/callback.php`
        
    - Copy the **Client Secret**.
        
3. **Example PHP Code:**
    
    Create a file `login.php` To initiate the SSO flow:
    
    ```
    <?php
    require 'vendor/autoload.php';
    use Jumbojett\OpenIDConnectClient;
    
    session_start();
    
    $oidc = new OpenIDConnectClient(
        'http://your_droplet_ip:8080/realms/master', // Keycloak provider URL
        'php-app',                                  // Client ID
        'your_client_secret_from_keycloak'          // Client Secret
    );
    
    // This triggers the authentication flow
    $oidc->authenticate();
    
    // Store user info in the session
    $_SESSION['user_info'] = $oidc->requestUserInfo();
    
    // Redirect to a protected page
    header("Location: /profile.php");
    exit();
    ```
    
    Create a `profile.php` To display user data:
    
    ```
    <?php
    session_start();
    if (empty($_SESSION['user_info'])) {
        header("Location: /login.php");
        exit();
    }
    $userInfo = $_SESSION['user_info'];
    echo "<h1>Welcome, " . htmlspecialchars($userInfo->name) . "</h1>";
    echo "<p>Email: " . htmlspecialchars($userInfo->email) . "</p>";
    ?>
    ```
