# Setting Up Docker Server

This guide is for setting up a docker container to host a laravel website. The steps involved are from installing the docker image, downloading the necessary dependecies within the container, and then finally, setting up the git connection.

## Docker Step

- [] Initializing the ubuntu docker container
  - `docker run -dit -p 80:80 ubuntu:latest`
- [] Entering the container and accessing its shell
  - `docker exec -it containes_name_or_id /bin/bash`

## Container Environment Setup

- [] Updates the package list (Good habit to do)
  - `apt update`
- [] Install the **HTTP server** nginx
  - `apt install nginx`
- [] Start the server manually since there is no init system
  - `/etc/init.d/nginx start`
- [] Install all the necessary packages for laravel file, editing, downloading, etc.
  - `apt install curl vim php-cli unzip php-fpm php-mysql php-mbstring php-xml php-bcmath php-curl php-gd`
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

## Git Step
