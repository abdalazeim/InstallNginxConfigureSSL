# Install Nginx and configure SSL for Odoo  HTTPS
# Step 1: Install Nginx
### sudo apt install nginx
# Remove default Nginx configurations.
### sudo rm /etc/nginx/sites-enabled/default
### sudo rm /etc/nginx/sites-available/default
# Step 2: Configure Nginx Reverse proxy for Odoo
# Create a new Nginx configuration for Odoo in the sites-available directory.
###  sudo nano /etc/nginx/sites-available/odoo.conf
# Copy and paste the following configuration, ensure that you change the server_name directory to match your domain name.
### upstream odooserver {
     server 127.0.0.1:8069;
}

server {
     listen 80;
     server_name domainname.com;

     access_log /var/log/nginx/odoo_access.log;
     error_log /var/log/nginx/odoo_error.log;

     proxy_read_timeout 720s;
     proxy_connect_timeout 720s;
     proxy_send_timeout 720s;
     proxy_set_header X-Forwarded-Host $host;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_set_header X-Forwarded-Proto $scheme;
     proxy_set_header X-Real-IP $remote_addr;

     location / {
        proxy_redirect off;
        proxy_pass http://odooserver;
     }

     location ~* /web/static/ {
         proxy_cache_valid 200 90m;
         proxy_buffering on;
         expires 864000;
         proxy_pass http://odooserver;
     }

     gzip_types text/css text/less text/plain text/xml application/xml application/json application/javascript;
     gzip on;
 }

# Hit Ctrl+X followed by Y and Enter to save the file and exit.
# To enable this newly created website configuration, symlink the file that you just created into the sites-enabled directory. 
### sudo ln -s /etc/nginx/sites-available/odoo.conf /etc/nginx/sites-enabled/odoo.conf
# Check your configuration and restart Nginx for the changes to take effect.
### sudo nginx -t
### sudo service nginx restart
# Step 3: Install and configure SSL for Odoo HTTPS
# HTTPS is a protocol for secure communication between a server (instance) and a client (web browser)
### sudo apt install python3-certbot-nginx
# Now we have installed Certbot by Let’s Encrypt for Ubuntu 20.04++, run this command to receive your certificates
### sudo certbot --nginx certonly
# Enter your email and agree to the terms and conditions, then you will receive the list of domains you need to generate SSL certificate.
# Redirect HTTP Traffic to HTTPS
### upstream odooserver {
     server 127.0.0.1:8069;
 }

 server {
     listen [::]:80;
     listen 80;

     server_name domainname.com www.domainname.com;

     return 301 https://domainname.com$request_uri;
 }

 server {
     listen [::]:443 ssl;
     listen 443 ssl;

     server_name www.domainname.com;

     ssl_certificate /etc/letsencrypt/live/domainname.com/fullchain.pem;
     ssl_certificate_key /etc/letsencrypt/live/domainname.com/privkey.pem;

     return 301 https://domainname.com$request_uri;
 }

 server {
     listen [::]:443 ssl http2;
     listen 443 ssl http2;

     server_name domainname.com;

     ssl_certificate /etc/letsencrypt/live/domainname.com/fullchain.pem;
     ssl_certificate_key /etc/letsencrypt/live/domainname.com/privkey.pem;

     access_log /var/log/nginx/odoo_access.log;
     error_log /var/log/nginx/odoo_error.log;

     proxy_read_timeout 720s;
     proxy_connect_timeout 720s;
     proxy_send_timeout 720s;
     proxy_set_header X-Forwarded-Host $host;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_set_header X-Forwarded-Proto $scheme;
     proxy_set_header X-Real-IP $remote_addr;

     location / {
        proxy_redirect off;
        proxy_pass http://odooserver;
     }

     location ~* /web/static/ {
         proxy_cache_valid 200 90m;
         proxy_buffering on;
         expires 864000;
         proxy_pass http://odooserver;
     }

     gzip_types text/css text/less text/plain text/xml application/xml application/json application/javascript;
     gzip on;
 }

 # Hit CTRL+X followed by Y to save the changes.
 # Check your configuration and restart Nginx for the changes to take effect. 
 ### sudo nginx -t
 ### sudo service nginx restart
 # Renewing SSL Certificate
 # Certificates provided by Let’s Encrypt are valid for 90 days only, so you need to renew them often. Now you set up a cronjob to check for the certificate which is due to  
 # expire in next 30 days and renew it automatically
 ### sudo crontab -e
 # Add this line at the end of the file 
 ### 0 0,12 * * * certbot renew >/dev/null 2>&1
 # Hit CTRL+X followed by Y to save the changes.
# This cronjob will attempt to check for renewing the certificate twice daily
- **ScrennShot**:

     ![ScrennShot](https://github.com/abdalazeim/InstallNginxConfigureSSL/blob/main/Install%20Nginx.png)

# That's All:
 - This Script By:  **☠αвɒ єℓαzєм☠**
 - Thanks For Usage :)
 - Have A Nice Day,GoodBye :)

 
