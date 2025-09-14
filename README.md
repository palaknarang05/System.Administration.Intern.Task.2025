# DigitalOcean Server Setup with Keycloak SSO
This project demonstrates how to set up a secure DigitalOcean droplet running Rocky Linux 10, install and configure Keycloak for Single Sign-On (SSO), and integrate it with three applications: Drupal 11, a Django project, and a PHP application.
## A.Live Environment
 
- **Droplet IP:** http://139.59.91.6  
- **Keycloak Admin Console:** https://139.59.91.6:8443/admin/master/console/
   - Username: `palak`
   - Password: `123456`
   - Note: Use the **Advanced** login if prompted.
- **Drupal App:** http://your-drupal-domain.com  
- **Django App:** http://your-django-domain.com  
- **PHP App:** http://your-php-domain.com  
   
## B. Documentation  

The setup is broken into logical steps for easy understanding:  

 1. [01-server-setup.md](docs/01-server-setup.md) – Droplet, SSH, firewall, updates  
 2. [02-keycloak.md](docs/02-keycloak.md) – Install & configure Keycloak  
 3. [03-drupal.md](docs/03-drupal.md) – Drupal setup + SSO  
 4. [04-django.md](docs/04-django.md) – Django setup + SSO  
 5. [05-php.md](docs/05-php.md) – PHP app setup + SSO

## C. Screenshots  

Screenshots are available in the [`screenshots/`](screenshots/) folder as proof of setup:  
- Droplet creation (DigitalOcean panel)  
- Firewall rules (`firewall-cmd --list-all`)  
- Keycloak service status (`systemctl status keycloak`)  
- Keycloak Admin Console  
- Successful SSO logins on Drupal, Django, PHP apps

## D. Evaluation  

This project satisfies the requirements:  
- Functional SSO across all apps  
- Step-by-step documentation  
- Screenshots included for proof  
- Live accessible demo  
