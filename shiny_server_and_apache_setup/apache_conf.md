# Apache reverse proxy configuration for shiny server

First you need to install apache and configure a virtual host with SSL enabled.

## Install and activate the `mod_proxy` of apache

```bash
# Insatall apache proxy
sudo apt-get install libapache2-mod-proxy-html libxml2-dev
# Activate mod_proxy
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod proxy_wstunnel
```

## Update your virtual host to use the reverse proxy

```apache
# ...
RewriteEngine on
RewriteCond %{HTTP:Upgrade} =websocket
RewriteRule /(.*) ws://localhost:3839/$1 [P,L]
RewriteCond %{HTTP:Upgrade} !=websocket
RewriteRule /(.*) http://localhost:3839/$1 [P,L]
ProxyPass / http://localhost:3839/
ProxyPassReverse / http://localhost:3839/
ProxyRequests Off
# ...
```
