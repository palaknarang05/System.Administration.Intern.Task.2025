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
sudo tee /etc/httpd/conf.d/drupal.conf >/dev/null <<'EOF'
<VirtualHost *:80>
    ServerName 139.59.91.6
    DocumentRoot /var/www/drupal

    <Directory /var/www/drupal/>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog  /var/log/httpd/drupal_error.log
    CustomLog /var/log/httpd/drupal_access.log combined
</VirtualHost>
EOF

sudo apachectl configtest
sudo systemctl restart httpd



sudo mkdir -p /var/www
cd /var/www
sudo composer create-project drupal/recommended-project drupal
sudo chown -R apache:apache /var/www/drupal
sudo find /var/www/drupal -type d -exec chmod 755 {} \;
sudo find /var/www/drupal -type f -exec chmod 644 {} \;

sudo mkdir -p /var/www/drupal/sites/default/files
sudo chown -R apache:apache /var/www/drupal/sites/default/files



sudo semanage fcontext -a -t httpd_sys_rw_content_t "/var/www/drupal/sites/default/files(/.*)?"
sudo restorecon -Rv /var/www/drupal/sites/default/files
sudo setsebool -P httpd_can_network_connect 1

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
  2. In Keycloak Admin Console:
    • Clients → Create client → ID: drupal
	• Valid redirect URIs: http://your_drupal_domain.com/keycloak/oauth2/callback
	• Copy Client Secret.
 3. Configure Drupal: Configuration → Web services → Keycloak → Add Keycloak URL, Realm, Client ID, Secret.
