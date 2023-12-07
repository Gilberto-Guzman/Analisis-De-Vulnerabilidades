# DVWA
  sudo apt update && sudo apt upgrade -y
  sudo apt -y install apache2 mariadb-server php php-mysqli php-gd libapache2-mod-php
  sudo mysql
  
  create database dvwa;
  use dvwa;
  
  create user 'admin'@'localhost' identified by 'password';
  grant all privileges on dvwa.* to 'admin'@'localhost' identified by 'password';
  exit
  
  sudo apt install git -y
  cd /var/www/html
  sudo git clone https://github.com/digininja/DVWA.git
  sudo mv DVWA dvwa
  sudo chown -P www-data:www-data /var/www/html/*
  sudo cp /var/www/html/dvwa/config/config.inc.php.dist /var/www/html/dvwa/config/config.inc.php
  cd dvwa/config
  sudo nano config.inc.php
  
  sudo systemctl restart apache2 && systemctl restart mysql
