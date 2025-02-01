# Setting up MariaDB for Laravel development

## Who is this guide for?

This guide is for someone who is starting out their journey in learning laravel and is unfamiliar with the process of connecting it to database within their local computer. This is also for those who use linux and mainly use CLI tools. For example, I am currently using Artix (Arch) linux, btw, and will be using the mariadb package. Let's get started!

## Steps 

- Installing Database packages from AUR.
  - `mariadb`
  - `mariadb-runit` -> This is necessary for Artix Linux users. With any other distros or Arch, then you can skip this. 
    - Create the necessary link file `sudo ln -s /etc/runit/sv/mariadb /run/runit/service/`
- Creating the database in within `/var/lib/mysql`
  - `mariadb-install-db --user=mysql --basedir=/usr --datadir=/var/lib/mysql`
- Some security initialization
  - `sudo mariadb-secure-installation`
- Connecting to the database to ensure a password for root so that laravel can access to the database.
  - Connect to the database
    - `mariadb -u root -p`
  - Alter the password
    - `ALTER USER 'root'@'localhost' IDENTIFIED BY 'your-super-secret-password';`
- Run `mariadb` in the backgroud
  - For Arch
    - `sudo systemd start mariadb` -> something along those lines.
  - For Artix
    - `sudo sv start mariadb`
  - For other
    - Idk man, figure it out. Google "How to make deamon run on my operating system?" or something along those lines.

That should be what is necessary for the database setup. Now, just some final setup on Laravel/php's side.

- Update `php.ini`
  - `php --ini` to know where the config file is located. Generally in `/etc/php/php.ini`
  - Uncomment this line `extension=pdo_mysql`.
- Update you project's `.env` file
  - ```
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=mysql
    DB_USERNAME=root
    DB_PASSWORD=your-secret-password
  ```
  
That should be it! You should no longer get errors on migrating into your database.

Thank you for listening to my TED talk.
