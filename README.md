## Digital Ocean Server Setup with Keycloak SSO

This guide provides a streamlined approach to setting up a **Digital Ocean droplet** with **Rocky Linux 10**, configuring **Keycloak** for Single Sign-On (SSO), and deploying three separate applications: **Drupal 11**, a **Django** project, and a generic **PHP** application.

---

## Live Deployment

- **Droplet IP (Doubles as a landing page with all the links)**: [https://165.22.209.0](https://165.22.209.0)  
- **Keycloak Login**: [https://165.22.209.0/keycloak](https://165.22.209.0/keycloak)
- **Drupal 11 Application**: [https://165.22.209.0/drupal](https://165.22.209.0/drupal)
- **PHP Application**: [https://165.22.209.0/php](https://165.22.209.0/php)
- **Django Application**: [https://165.22.209.0/django](https://165.22.209.0/django)  

---

## Documentation

The setup is broken down into the following sections. Each section has its own markdown file with detailed steps:


---

## Technology Stack

- **Server**: DigitalOcean Droplet (Rocky Linux 10)  
- **Web Server**: Apache HTTPD with `mod_ssl`, `mod_proxy`, and `mod_proxy_wstunnel`  
- **Authentication**: Keycloak (OpenID Connect)  
- **Application**: Django (Python), Drupal, PHP
- **Database**: MariaDB

---

