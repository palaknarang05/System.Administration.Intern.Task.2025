# 04 – Django Setup & Keycloak SSO
## A. Create Database
```sql
sudo mysql -u root -p
CREATE DATABASE djangodb;
CREATE USER 'djangouser'@'localhost' IDENTIFIED BY 'another_secure_password';
GRANT ALL PRIVILEGES ON djangodb.* TO 'djangouser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
## B. Set Up Django Project
```bash
sudo mkdir /var/www/django_project
sudo chown your_username:your_username /var/www/django_project
cd /var/www/django_project
python3 -m venv venv
source venv/bin/activate

pip install django gunicorn mozilla-django-oidc mysqlclient

django-admin startproject mysite .
python manage.py migrate
python manage.py createsuperuser
deactivate
```
## C. Apache Reverse Proxy
Create /etc/httpd/conf.d/django.conf:
```apache
<VirtualHost *:80>
    ServerName your_django_domain.com
    ProxyPass / http://127.0.0.1:8000/
    ProxyPassReverse / http://127.0.0.1:8000/
</VirtualHost>
```
```bash
sudo systemctl restart httpd
```
## D. Integrate Keycloak
In Django settings.py:
```python
INSTALLED_APPS = [
    'mozilla_django_oidc',
    # ...
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
In urls.py:

```python
from django.urls import path, include
from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls),
    path('oidc/', include('mozilla_django_oidc.urls')),
]
```

