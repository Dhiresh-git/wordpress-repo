# wordpress-repo
Install Wordpress on Ubuntu 22.04 LTS AWS | #wordpress #MySQL #NGINX

- Update package index on Linux Ubuntu 22.04LTS
- Install PHP
- Check the installed PHP version
- Download Wordpress
- Give permissions to Wordpress folder
- Install mysql 
- Create new database in MySQL
- Add a new MySQL database user with privileges
- Give privileges to newly added users in MySQL DB
- Configure NGINX
- Install Wordpress by installer
- Install SSL certificates to secure website
- Test Wordpress website

Wordpress install
- Update packages:
sudo apt update && sudo apt upgrade -y

Create a non-root user with sudo privileges for better security:
 sudo adduser Dhiresh
 sudo usermod -aG sudo Test@123


- Install PHP:
sudo apt install -y nginx php-dom php-simplexml php-ssh2 php-xml php-xmlreader php-curl php-exif php-ftp php-gd php-iconv php-imagick php-json php-mbstring php-posix php-sockets php-tokenizer php-fpm php-mysql php-gmp php-intl php-cli

- Check version
https://wordpress.org/latest.tar.gz
Configure PHP:
Path:  sudo vim /etc/php/*/fpm/php.ini

upload_max_filesize = 200M
post_max_filesize = 500M
memory_limit = 512M
cgi.fix_pathinfo = 0
max_execution_time = 360

Save the file, start and enable PHP-FPM.
sudo systemctl restart php*-fpm.service

Verify if the service is running:
systemctl status php*-fpm.service
========================================
Download WordPress
wget https://wordpress.org/latest.tar.gz

Extract the file and move it to the /var/www directory
tar -xvzf latest.tar.gz
sudo mv wordpress /var/www/wordpress

Correct permissions for the directory:
sudo chown -R www-data:www-data /var/www/wordpress/
sudo chmod -R 755 /var/www/wordpress/

=========================================
Database
sudo apt install mysql-server

sudo mysql_secure_installation

sudo mysql
CREATE DATABASE wordpressdb;
CREATE USER 'wordpressuser'@'localhost' IDENTIFIED BY 'Test@123';
GRANT ALL PRIVILEGES ON wordpressdb.* TO 'wordpressuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;


GRANT ALL PRIVILEGES ON wordpressdb.* TO 'wordpressuser'@'localhost' WITH GRANT OPTION;
=========================================
Configure Nginx Web server 
Path: sudo nano /etc/nginx/site-enabled/wordpress

server {
    listen 80;
    listen [::]:80;
    server_name 3.110.101.179 3.110.101.179;
    root /var/www/wordpress;
    index  index.php index.html index.htm;
    access_log /var/log/nginx/wpress_access.log;
    error_log /var/log/nginx/wpress_error.log;

    client_max_body_size 100M;
    autoindex off;
    location / {
        try_files $uri $uri/ /index.php?$args;
    }

    location ~ \.php$ {
         include snippets/fastcgi-php.conf;
         fastcgi_pass unix:/var/run/php/php-fpm.sock;
         fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
         include fastcgi_params;
    }
}

Note - Remember to replace example.com with your own domain name and /var/run/php/php7.4-fpm.sock with /var/run/php/php8.0-fpm.sock for PHP 8.0. Save the file and exit.

Check the syntax of the file:
sudo nginx -t

Restart:
sudo systemctl restart nginx

=========================================
This part is pending because of domain.If you have domain then follow this steps,
Access the WordPress Web Installer
Install Wordpress
=========================================
Step 1 — Installing Certbot
sudo snap install core; sudo snap refresh core

Remove if ready installed
sudo apt remove certbot

sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot

- Obtain certificate
sudo certbot --nginx -d example.com -d www.example.com

Step 3 — Allowing HTTPS Through the Firewall
sudo ufw status

Step 5 — Verifying Certbot Auto-Renewal
sudo systemctl status snap.certbot.renew.service
sudo certbot renew --dry-run

==============================================================================

Optimize the Nginx server configuration to enhance website performance (e.g.,caching, gzip compression, etc.).

1. Gzip Compression and Browser Caching:
   Edit the Nginx configuration file:

sudo nano /etc/nginx/nginx.conf

http {
    # ... other configurations ...

    gzip on;
    gzip_types application/javascript image/* text/css;
    gzip_min_length 1000;

    # ... other configurations ...

    server {
        # ... server configurations ...

        location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
            expires 1y;
            add_header Cache-Control "public, max-age=31536000";
            access_log off;
        }

        # ... other location blocks ...

        # ... server configurations ...
    }
}
