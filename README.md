## Digital Ocean Server Setup with Keycloak SSO

This repository has my approach to setting up a **Digital Ocean droplet** with **Rocky Linux 10**, configuring **Keycloak** for Single Sign-On (SSO), and deploying three separate applications: **Drupal 11**, a **Django** project, and a generic **PHP** application.

---


## Documentation

The setup is broken down into the following sections. Each section has its own markdown file with detailed steps:

- [Server Setup](https://github.com/DhawalKachhwaha/Screening-Task/blob/main/docs/01-server-setup.md)
- [Keycloak Setup](https://github.com/DhawalKachhwaha/Screening-Task/blob/main/docs/02-keycloak-setup.md)
- [Drupal 11 Setup](https://github.com/DhawalKachhwaha/Screening-Task/blob/main/docs/03-drupal-setup.md)
- [PHP Webapp Setup](https://github.com/DhawalKachhwaha/Screening-Task/blob/main/docs/04-php-setup.md)
- [Django Setup](https://github.com/DhawalKachhwaha/Screening-Task/blob/main/docs/05-django-setup.md)

---

## Technology Stack

- **Server**: DigitalOcean Droplet (Rocky Linux 10)  
- **Web Server**: Apache HTTPD with `mod_ssl`, `mod_proxy`, and `mod_proxy_wstunnel`  
- **Authentication**: Keycloak (OpenID Connect)  
- **Application**: Django (Python), Drupal, PHP
- **Database**: MariaDB

---

