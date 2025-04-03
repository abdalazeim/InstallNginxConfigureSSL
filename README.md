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

