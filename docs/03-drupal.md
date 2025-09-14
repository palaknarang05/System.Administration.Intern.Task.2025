# 03 – Drupal Setup & Keycloak SSO
## A. Create Database
```sql
sudo mysql -u root -p
CREATE DATABASE drupaldb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'drupaluser'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON drupaldb.* TO 'drupaluser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
## B. Install Drupal
```bash
cd /var/www/
sudo dnf install composer -y
sudo composer create-project drupal/recommended-project drupal
sudo chown -R apache:apache /var/www/drupal
sudo chmod -R 755 /var/www/drupal/web
```
## C. Configure Apache
Create /etc/httpd/conf.d/drupal.conf:
```apache
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
```bash
sudo systemctl restart httpd
```
## D. Integrate Keycloak SSO
```bash
# Install Keycloak Drupal module
sudo composer require drupal/keycloak
```
  1. Enable Keycloak module in Drupal UI (Extend > Keycloak).
	2.	In Keycloak Admin Console:
	•	Clients → Create client → ID: drupal
	•	Valid redirect URIs: http://your_drupal_domain.com/keycloak/oauth2/callback
	•	Copy Client Secret.
	3.	Configure Drupal: Configuration → Web services → Keycloak → Add Keycloak URL, Realm, Client ID, Secret.
