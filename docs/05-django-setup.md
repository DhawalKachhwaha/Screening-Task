### 2. Django Project Setup & SSO

## No major changes from my side

**A. Create Database:**

```
sudo mysql -u root -p
CREATE DATABASE djangodb;
CREATE USER 'djangouser'@'localhost' IDENTIFIED BY 'another_secure_password';
GRANT ALL PRIVILEGES ON djangodb.* TO 'djangouser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**B. Set Up Django Project with Gunicorn:**

```
# Create project directory and virtual environment
sudo mkdir /var/www/django_project
sudo chown your_username:your_username /var/www/django_project
cd /var/www/django_project
python3 -m venv venv
source venv/bin/activate

# Install Django, Gunicorn, and a robust OIDC library
pip install django gunicorn mozilla-django-oidc mysqlclient

# Create project and configure database in settings.py
django-admin startproject mysite .
# ... edit mysite/settings.py to configure your MariaDB database ...
python manage.py migrate
python manage.py createsuperuser
deactivate
```

**C. Configure Apache as a Reverse Proxy:**

Create `/etc/httpd/conf.d/django.conf`:

```
<VirtualHost *:80>
    ServerName your_django_domain.com
    ProxyPass / [http://127.0.0.1:8000/](http://127.0.0.1:8000/)
    ProxyPassReverse / [http://127.0.0.1:8000/](http://127.0.0.1:8000/)
</VirtualHost>
```

For this to work, you'll run Gunicorn listening on port 8000. Create a `systemd` service for Gunicorn to manage it properly. Restart Apache after setup.

**D. Integrate Keycloak SSO with `mozilla-django-oidc`:**

1. **Create Keycloak Client:**
    
    - **Client ID:** `django`
        
    - **Valid redirect URIs:** `http://your_django_domain.com/oidc/callback/`
        
    - Copy the **Client Secret**.
        
2. **Configure Django (`settings.py`):**
    
    ```
    # settings.py
    INSTALLED_APPS = [
        # ...
        'mozilla_django_oidc',
    ]
    
    AUTHENTICATION_BACKENDS = [
        'mozilla_django_oidc.auth.OIDCAuthenticationBackend',
        'django.contrib.auth.backends.ModelBackend',
    ]
    
    OIDC_RP_CLIENT_ID = "django"
    OIDC_RP_CLIENT_SECRET = "your_client_secret_from_keycloak"
    OIDC_OP_AUTHORIZATION_ENDPOINT = "http://your_droplet_ip:8080/realms/master/protocol/openid-connect/auth"
    OIDC_OP_TOKEN_ENDPOINT = "http://your_droplet_ip:8080/realms/master/protocol/openid-connect/token"
    OIDC_OP_USER_ENDPOINT = "http://your_droplet_ip:8080/realms/master/protocol/openid-connect/userinfo"
    OIDC_RP_SIGN_ALGO = "RS256"
    OIDC_OP_JWKS_ENDPOINT = "http://your_droplet_ip:8080/realms/master/protocol/openid-connect/certs"
    
    LOGIN_REDIRECT_URL = "/"
    LOGOUT_REDIRECT_URL = "/"
    ```
    
3. **Update URLs (`urls.py`):**
    
    ```
    # urls.py
    from django.urls import path, include
    from django.contrib import admin
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('oidc/', include('mozilla_django_oidc.urls')),
        # ... your other app urls
    ]
    ```
    

Restart Gunicorn and Apache to apply changes.

### Shortcomings 
- redirect after login not working correctly 
