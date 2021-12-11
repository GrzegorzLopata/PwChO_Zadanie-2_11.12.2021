# PwChO_Zadanie-2_11.12.2021

# Wersja docker-compose
version: '3.7'
# Wykorzystywane usługi 
services:
# Serwer PHP 7.3, port 80 - przyłączony do sieci backend
    php-httpd:
        image: php:7.3-apache
        ports:
            - 80:80
        volumes:
            - "./DocumentRoot:/var/www/html"
        networks:
            - backend
# MySQL Serwer z hasłem root-a rootpassword, port 3306 (domyślny port SQL-owy) - przyłączony do sieci backend
    mysql_db_container:
        image: mysql:latest
        command: --default-authentication-plugin=mysql_native_password
        environment:
          MYSQL_ROOT_PASSWORD: rootpassword
          MYSQL_PASSWORD: 'testpassword'
          MYSQL_DATABASE: 'testdb'
        ports:
            - 3306:3306
        volumes:
            - mysql_db_data_container:/var/lib/mysql
        networks:
            - backend
# PHPMyAdmin działający na serwerze SQL mysql_db_container, port podany w zadaniu 6001 - przyłączony do sieci backend
     phpmyadmin:
        image: phpmyadmin/phpmyadmin
        links:
            - 'mysql_db_container:db'
        ports:
            - 6001:80
        networks:
            - backend
# Serwer nginx przyłączony do sieci backend oraz frontend, port 6666 z zadania, służący do wyświetlenia pliku index.php 
# (zawartość pliku index.php: funkcja phpinfo();  
      nginx_web:
         image: nginx:latest
         volumes:
              - ./templates:/etc/nginx/templates
         ports:
              - "6666:80"
          environment:
              - NGINX_HOST=index.php
              - NGINX_PORT=6666
          networks:
              - backend
              - frontend
# Wolumen konteneru dla serweru MySQL    
volumes:
  mysql_db_data_container:
# Definicja dwóch użytych sieci podczas tworzenia docker-compose.yml
networks:
  - backend:
  - frontend:

# Uruchomienie docker-compose.yml
docker-compose up -d
