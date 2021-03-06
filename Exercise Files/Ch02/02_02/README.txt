# RUN THESE COMMANDS ON YOUR LOCAL WORKSTATION
    # Start the virtual machine and log in
    vagrant up
    vagrant ssh

# Nginx is installed and configured for you in this lesson.
# Proceed with the following steps to complete the configuration.

# RUN THESE COMMANDS ON THE VIRTUAL MACHINE
    sudo su -

# Update the apt cache and install PHP
	apt update
	apt install -y php-fpm php-mysql

# Once the installation is complete, check the version of PHP:
    php --version

# Check the PHP FastCGI Process Manager:
    systemctl status php7.2-fpm

# Open /etc/nginx/conf.d/wisdompetmed.local.conf for editing:
    vim /etc/nginx/conf.d/wisdompetmed.local.conf

# Edit /etc/nginx/conf.d/wisdompetmed.local.conf so that is has the
# following contents and then save the file:
    server {
        listen 80 default_server;

        root /var/www/wisdompetmed.local;

        server_name wisdompetmed.local www.wisdompetmed.local;

        index index.html index.htm index.php;

        access_log /var/log/nginx/wisdompetmed.local.access.log;
        error_log /var/log/nginx/wisdompetmed.local.error.log;

        location / {
            # First attempt to serve request as file, then
            # as directory, then fall back to displaying a 404.
            try_files $uri $uri/ =404;
        }

        location /images/ {
            # Allow the contents of the /image folder to be listed
            autoindex on;
            access_log /var/log/nginx/wisdompetmed.local.images.access.log;
            error_log /var/log/nginx/wisdompetmed.local.images.error.log;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
            fastcgi_intercept_errors on;
        }

        error_page 404 /404.html;
        location = /404.html {
            internal;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            internal;
        }

        location = /500 {
            fastcgi_pass unix:/this/will/fail;
        }
    }

# Now continue with the following commands to test and reload the configuration:
    nginx -t
    systemctl reload nginx

# Create a PHP info page in the root directory of the demo site:
	vim /var/www/wisdompetmed.local/info.php

# Add the following contents:
    <?php phpinfo(); phpinfo(INFO_MODULES); ?>

# Load the info.php page in a browser or via curl: http://192.168.0.3/info.php
