# 05 – PHP Setup & Keycloak SSO
## A. Deploy PHP Application
```bash
sudo mkdir /var/www/php_app
# Copy your PHP application files here
sudo chown -R apache:apache /var/www/php_app
sudo chmod -R 755 /var/www/php_app
```
## B. Apache Configuration
Create /etc/httpd/conf.d/php_app.conf:
```apache
<VirtualHost *:80>
    ServerName your_php_app_domain.com
    DocumentRoot /var/www/php_app

    <Directory /var/www/php_app>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```
```bash
sudo systemctl restart httpd
```
## C. Integrate Keycloak
```bash
cd /var/www/php_app
sudo composer require jumbojett/openid-connect-php
```
login.php:

```php
<?php
require 'vendor/autoload.php';
use Jumbojett\OpenIDConnectClient;

session_start();

$oidc = new OpenIDConnectClient(
    'http://your_droplet_ip:8080/realms/master',
    'php-app',
    'your_client_secret_from_keycloak'
);

$oidc->authenticate();
$_SESSION['user_info'] = $oidc->requestUserInfo();
header("Location: /profile.php");
exit();
```
profile.php:

```php
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
