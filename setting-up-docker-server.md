# Setting Up Docker Server

This guide is for setting up a docker container to host a laravel website. The steps involved are from installing the docker image, downloading the necessary dependecies within the container, and then finally, setting up the git connection.

## Docker Step

- [] Initializing the ubuntu docker container
  - `docker run -dit -p 80:80 ubuntu:latest`
- [] Entering the container and accessing its shell
  - `docker exec -it container_name_or_id /bin/bash`

## Container Environment Setup

- [] Updates the package list (Good habit to do)
  - `apt update`
- [] Install the **HTTP server** nginx
  - `apt install nginx`
- [] Start the server manually since there is no init system
  - `/etc/init.d/nginx start`
- [] Install all the necessary packages for laravel file, editing, downloading, etc.
  - `apt install curl vim cron redis-server php-cli unzip php-fpm php-mysql php-mbstring php-xml php-bcmath php-curl php-gd`
- [] Install `composer`
  - `curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php`
  - `php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer`
- [] Setup mysql

  - [] Installation
    - `apt install mysql-server`
    - `/etc/init.d/mysql start`
  - [] Installation
    - `apt install mysql-server`
    - `/etc/init.d/mysql start`
  - [] Configuration
    - `mysql`
    - `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'your-password';` -> in of mysql.
    - `mysql_secure_installation` -> out of mysql.
    - `mysql -u root -p`
    - `CREATE DATABASE database-name;`
    - `CREATE USER 'username'@'%' IDENTIFIED WITH mysql_native_password BY 'your-password';` -> this is the user who only has access for the database created above.
    - `GRANT ALL ON database-name.* TO 'ourappuser'@'%';`
  - [] Copy the website files into docker
    - `docker cp /path/to/your/sourcecode container_name_or_id:/var/www/folder_name`
  - [] Configure `nginx`

    - `cd /etc/nginx/sites-available`
    - Change the `default` file in `/etc/nginx/sites-available`

      - ```
          server {
              listen 80 default_server;
              listen [::]:80 default_server;
              root /var/www/ourapp/public;

              add_header X-Frame-Options "SAMEORIGIN";
              add_header X-XSS-Protection "1; mode=block";
              add_header X-Content-Type-Options "nosniff";
              client_max_body_size 10M;

              index index.html index.htm index.php;

              charset utf-8;

              location / {
                  try_files $uri $uri/ /index.php?$query_string;
              }

              location = /favicon.ico { access_log off; log_not_found off; }
              location = /robots.txt  { access_log off; log_not_found off; }

              error_page 404 /index.php;

              location ~ \.php$ {
                  fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
                  fastcgi_index index.php;
                  fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
                  include fastcgi_params;
              }

              location ~ /\.(?!well-known).* {
                  deny all;
              }
          }
        ```

    - `/etc/init.d/nginx restart`
    - `/etc/init.d/php*-fpm start` -> This is the php server (If you make changes to php.ini file, then this needs to be restarted)
    - `chown -R www-data:www-data storage` -> giving some sort of access to the storage folder
    - Confirm database config in the `.env` file

## Configuring CRON

- [] Opening CRON config file
  - `crontab -e`
- [] Running queues example
  - `* * * * * /usr/bin/php /var/www/ourapp/artisan queue:work --max-time=60` -> Run every minute and the maximum time the command can run is 60 seconds
- [] Running a scheduled task example
  - `* * * * * /usr/bin/php /var/www/ourapp/artisan scheduled:run` -> Run the command every minute
- [] Start CRON
  - `/etc/init.d/cron start`

## Setting up Redis

- [] Install laravel requirment to use redis
  - `composer require predis/predis`

## Git Step
