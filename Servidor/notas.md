# Configuración Inicial del Servidor
    
    sudo apt update
    sudo apt upgrade
    sudo ufw app list
    sudo ufw allow OpenSSH
    sudo ufw enable

    sudo apt install apache2
    sudo ufw app list
    sudo ufw allow in "Apache Full"
    sudo ufw status
    sudo apt install php-mbstring php-xml php-bcmath php-mysql php-intl php-curl unzip git

    sudo apt install mysql-server
    mysql -u root -p
    CREATE DATABASE trabajosunach_db CHARACTER SET utf8 COLLATE utf8_spanish_ci;
    SHOW DATABASES;
    ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'Eq_VdeRwx79e46i';
    FLUSH PRIVILEGES;
    EXIT;

    sudo apt install php libapache2-mod-php
    php -v

    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"
    mv composer.phar /usr/bin/composer
    composer

    cd /var/www/
    git clone https://github.com/Gilberto-Guzman/trabajosunach
    rm -rf html
    mv trabajosunach/ html
    cd html
    composer install
    cp .env.example .env
    php artisan key:generate
    sudo nano .env

    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=trabajosunach
    DB_USERNAME=root
    DB_PASSWORD=Eq_VdeRwx79e46i


    --- ??? ---
    MAIL_DRIVER=smtp
    MAIL_HOST=smtp.gmail.com
    MAIL_PORT=587
    MAIL_USERNAME=ejemplo@gmail.com
    MAIL_PASSWORD="ejemplo"
    MAIL_ENCRYPTION=tls
    MAIL_FROM_ADDRESS="ejemplo@gmail.com"
    MAIL_FROM_NAME="${APP_NAME}"
    MAIL_ENCRYPTION=ssl

    php artisan migrate

    sudo nano /etc/apache2/sites-available/000-default.conf

    <VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html/public
        <Directory />
            Options FollowSymLinks
            AllowOverride None
        </Directory>
        <Directory /var/www/html>
            AllowOverride All
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>

    sudo systemctl reload apache2

    sudo a2enmod rewrite
    systemctl restart apache2
    chown -R www-data:www-data /var/www/html
    sudo chown -R $USER:www-data storage
    sudo chown -R $USER:www-data bootstrap/cache
    chmod -R 775 storage
    chmod -R 775 bootstrap/cache

    php artisan storage:link
    php artisan optimize:clear
    sudo systemctl reload apache2
    php artisan route:cache
    php artisan view:cache
    php artisan db:seed
    
    sudo apt install npm
    npm install
    npm install vite
    npm audit fix --force
    npm run build

# Parches de Seguridad