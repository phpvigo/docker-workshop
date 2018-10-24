# Docker Workshop

## Instalación docker CE

- Ubuntu: https://docs.docker.com/install/linux/docker-ce/ubuntu/
- Windows: https://store.docker.com/editions/community/docker-ce-desktop-windows
- Mac: https://store.docker.com/editions/community/docker-ce-desktop-mac
- Legacy:
  - Windows: https://docs.docker.com/toolbox/toolbox_install_windows/
  - Mac: https://docs.docker.com/toolbox/toolbox_install_mac/

# Timeline

> \> tty 1
```bash
$ sudo groupadd docker  	
$ sudo usermod -aG docker $USER
```

> \> tty 1
```bash
$ cd $HOME
$ mkdir workshopDocker-phpVigo && cd workshopDocker-phpVigo
$ mkdir docker && cd docker
```

> \> tty 1
```bash
$ docker run hello-world
```

> \> tty 1
```bash
$ docker ps
$ docker ps -a
```

> \> tty 1
```bash
$ docker run ubuntu
$ docker ps
$ docker ps -a
$ docker images
$ docker run -it ubuntu
root@f709b1535b47:/#
```

> \> tty 2
```bash
$ cd $HOME/workshopDocker-phpVigo/docker
$ docker run -it node
```

> \> tty 3
```bash
$ cd $HOME/workshopDocker-phpVigo/docker
$ docker ps
```

> \> tty 1
```bash
root@f709b1535b47:/# top
```

> \> tty 3
```bash
$ ps afux
```

> \> tty 1
```bash
root@f709b1535b47:/# Ctrl+C
root@f709b1535b47:/# exit
$ docker run --name=php-fpm php:7.2-fpm
```
 
> \> tty 2
```bash
$ Ctrl+C
$ docker exec php-fpm php -v
```
 
> \> tty 2
```bash
$ docker run -p 8080:80 -it --name=php-apache php:7.2-apache
```
 
> \> tty 3
```bash
$ docker exec -it php-apache bash
root@be8415f43808:/var/www/html# cat > index.php
```

```php
<?php 
phpinfo();
```

```bash
$ Ctrl+C
$ exit
```

> \> tty 1
```bash
$ Ctrl+C
$ docker run -it --name=php-fpm php:7.2-fpm
$ docker start php-fpm
```

> \> tty 1
```bash
$ docker ps
```

> \> tty 1
```bash
$ mkdir php-base && cd php-base
$ touch Dockerfile
$ vim Dockerfile
```

> workshopDocker-phpVigo/docker/php-base/Dockerfile

```dockerfile
FROM php:7.2-fpm

WORKDIR "/application"

RUN apt-get update \
&& apt-get install -y libicu-dev libpng-dev libjpeg-dev libpq-dev  \
&& apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
&& docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
&& docker-php-ext-install intl gd mbstring

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
```

> \> tty 1
```bash
$ docker build -t "php-fpm-base" .
$ docker run --name="php-fpm-base" php-fpm-base
```

> \> tty 3
```bash
$ docker exec php-fpm-base php -i
$ docker exec php-fpm-base composer -V
```

```bash
$ mkdir php-xdebug && cd php-xdebug
$ touch Dockerfile
$ vim Dockerfile
```

> workshopDocker-phpVigo/docker/php-xdebug/Dockerfile
```dockerfile
FROM php-fpm-base

# install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
```

> \> tty 3
```bash
$ docker build -t "php-fpm-xdebug" .
$ docker run --name="php-fpm-xdebug" php-fpm-xdebug
```

> \> tty 1
```bash
$ Ctrl+C
$ docker exec php-fpm-xdebug php -v
```

> \> tty 1
```bash
$ cd .. && mkdir php-dev && cd php-dev
$ touch Dockerfile
$ vim Dockerfile
```

> workshopDocker-phpVigo/docker/php-dev/Dockerfile
```dockerfile
FROM php-fpm-base

# install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
    
RUN apt-get update \
    && apt-get -y install unzip zlib1g-dev \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-install zip
```

> \> tty 1
```bash
$ docker build -t "php-fpm-dev" .
$ docker run --name="php-fpm-dev" php-fpm-dev
```

> \> tty 3
```bash
$ Ctrl+C
$ cd .. && mkdir php-dev-mysql && cd php-dev-mysql
$ touch Dockerfile
$ vim Dockerfile
```

> workshopDocker-phpVigo/docker/php-dev-mysql/Dockerfile
```dockerfile
FROM php-fpm-dev

# Install mysql
RUN apt-get update && apt-get install -y mysql-client \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-install mysqli pdo pdo_mysql
```

> \> tty 3
```bash
$ docker build -t "php-fpm-dev-mysql" .
$ docker run --name="php-fpm-dev-mysql" php-fpm-dev-mysql
```

> \> tty 3
```bash
$ Ctrl+C
```

> \> tty 2
```bash
$ Ctrl+C
```

> \> tty 1
```bash
$ Ctrl+C
$ cd ..
$ git init
$ git config --global user.email "rolando.caldas@gmail.com"
$ git config --global user.name "Rolando Caldas"
$ git add .
$ git commit -m "My php docker containers configuration for development"
$ git remote add origin https://github.com/rolando-caldas/docker-php.git
$ git push -u origin master
```

> \> tty 1
```bash
$ vim php-xdebug/Dockerfile
```

> workshopDocker-phpVigo/docker/php-xdebug/Dockerfile
```dockerfile
FROM rolandocaldas/php:7.2

# install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
```

> \> tty 1
```bash
$ vim php-dev/Dockerfile
```

> workshopDocker-phpVigo/docker/php-dev/Dockerfile
```dockerfile
FROM rolandocaldas/php:7.2

# install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
    
RUN apt-get update \
    && apt-get -y install unzip zlib1g-dev \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-install zip
```

> \> tty 1
```bash
$ vim php-dev-mysql/Dockerfile
```

> workshopDocker-phpVigo/docker/php-dev-mysql/Dockerfile
```dockerfile
FROM rolandocaldas/php:7.2-dev

# Install mysql
RUN apt-get update && apt-get install -y mysql-client \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-install mysqli pdo pdo_mysql
```

> \> tty 1
```bash
$ git add .
$ git commit -m "Changed Dockerfiles to set the FROM new value"
$ git push origin master
```

> \> tty 1
```bash
$ docker run rolandocaldas/php:7.2-dev-mysql
```

> \> tty 1
```bash
$ Ctrl+C
$ cd ..
$ mkdir infrastructure && cd infrastructure/
```

> \> tty 1
```bash
$ touch docker-compose.yml
$ vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose-yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
```

> \> tty 1
```bash
$ docker-compose up -d
```

> \> tty 1
```bash
$ docker-compose stop
$ vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose-yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
```

> \> tty 1
```bash
$ docker-compose up -d
$ docker-compose exec webserver ping php-fpm
```

> \> tty 1
```bash
$ docker-compose stop
$ vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose-yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
  mysql:    
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=database
      - MYSQL_USER=user
      - MYSQL_PASSWORD=pwd
```

> \> tty 1
```bash
$ docker-compose up -d
$ docker-compose exec php-fpm mysql -h mysql -uuser -p
#  MySQL [(none)]> use database;   
#  Database changed
#  MySQL [database]> quit
#  Bye
$ 
```

> \> tty 1
```bash
$ vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose-yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
  mysql:    
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=database
      - MYSQL_USER=user
      - MYSQL_PASSWORD=pwd
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
    environment:
      - PMA_USER=user
      - PMA_PASSWORD=pwd
      - PMA_HOST=mysql
```

> \> tty 1
```bash
$ docker-compose stop && docker-compose up -d
```

> \> tty 1
```bash
$ vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose-yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
    depends_on:
      - mysql
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
    - php-fpm
  mysql:    
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=database
      - MYSQL_USER=user
      - MYSQL_PASSWORD=pwd
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
    environment:
      - PMA_USER=user
      - PMA_PASSWORD=pwd
      - PMA_HOST=mysql
    depends_on:
      - mysql
```

> \> tty 1
```bash
$ docker-compose stop
$ docker-compose up webserver
```

> \> tty 1
```bash
$ Ctrl+C
$ mkdir database
$ vim docker-compose-yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose.yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
    depends_on:
      - mysql
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
      - php-fpm
  mysql:    
    image: mysql:5.7
    volumes:
      - "./database:/var/lib/mysql"    
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=database
      - MYSQL_USER=user
      - MYSQL_PASSWORD=pwd
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
    environment:
      - PMA_USER=user
      - PMA_PASSWORD=pwd
      - PMA_HOST=mysql
    depends_on:
      - mysql
```

> \> tty 1
```bash
$ docker-compose stop && docker-compose up -d --build
$ docker-compose stop
$ docker rm infrastructure_mysql_1
$ docker-compose up -d --build
$ ls -lha ./database
```

> \> tty 1
```bash
$ docker-compose exec mysql id mysql
```

> \> tty 1
```bash
$ id
```

> \> tty 1
```bash
$ mkdir mysql && cd mysql
$ touch Dockerfile && vim Dockerfile
```

> \> workshopDocker-phpVigo/docker/infrastructure/mysql/Dockerfile
```dockerfile
FROM mysql:5.7

RUN usermod -u 1000 mysql && groupmod -g 1000 mysql
```

> \> tty 1
```bash
$ cd .. && vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose.yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
    depends_on:
      - mysql
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
      - php-fpm
  mysql:    
    build:
      context: .
      dockerfile: mysql/Dockerfile
    volumes:
      - "./database:/var/lib/mysql"    
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=database
      - MYSQL_USER=user
      - MYSQL_PASSWORD=pwd
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
    environment:
      - PMA_USER=user
      - PMA_PASSWORD=pwd
      - PMA_HOST=mysql
    depends_on:
      - mysql
```

> \> tty 1
```bash
$ docker-compose stop && docker-compose up -d --build
```

> \> tty 1
```bash
$ docker-compose exec mysql id mysql
$ ls -la 
```

> \> tty 1
```bash
$ vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose.yml
```yaml
version: "3.5"
services:
  php-fpm:
    image: rolandocaldas/php:7.2-dev-mysql
    depends_on:
      - mysql
    volumes:
      - ../application:/application
      - ./php-fpm/php-ini-overrides.ini:/usr/local/etc/php/conf.d/infrastructure-overrides.ini        
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
      - php-fpm
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
      - ../application:/application
  mysql:    
    build:
      context: .
      dockerfile: mysql/Dockerfile
    volumes:
      - "./database:/var/lib/mysql"    
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=database
      - MYSQL_USER=user
      - MYSQL_PASSWORD=pwd
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
    environment:
      - PMA_USER=user
      - PMA_PASSWORD=pwd
      - PMA_HOST=mysql
    depends_on:
      - mysql
```

> \> tty 1
```bash
$ mkdir php-fpm && cd php-fpm
$ touch php-ini-overrides.ini && vim php-ini-overrides.ini
```

> \> workshopDocker-phpVigo/docker/infrastructure/php-fpm/php-ini-overrides.ini
```ini
upload_max_filesize = 100M
post_max_size = 108M
```

> \> tty 1
```bash
$ cd .. && mkdir nginx && cd nginx
$ touch nginx.conf && vim nginx.conf
```

> \> workshopDocker-phpVigo/docker/infrastructure/nginx/nginx.conf
```
server {
    listen 80 default;

    client_max_body_size 108M;

    access_log /var/log/nginx/application_access.log;
    error_log /var/log/nginx/application_error.log;

    root /application/public;
    index index.php;
    if (!-e $request_filename) {
        rewrite ^.*$ /index.php last;
    }

    location ~ \.php$ {
        fastcgi_pass php-fpm:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_buffers 16 16k;
        fastcgi_buffer_size 32k;
        include fastcgi_params;
    }    
}
```

> \> tty 1
```bash
$ cd .. && mkdir ../application && cd php-fpm/
$ touch Dockerfile && vim Dockerfile
```

> workshopDocker-phpVigo/docker/infrastructure/php-fpm/Dockerfile
```dockerfile
FROM rolandocaldas/php:7.2-dev-mysql

RUN usermod -u 1000 www-data && groupmod -g 1000 www-data
```

> \> tty 1
```bash
$ cd ../ && vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose.yml
```yaml
version: "3.5"
services:
  php-fpm:
    build:
      context: .
      dockerfile: php-fpm/Dockerfile
    depends_on:
      - mysql
    volumes:
      - ../application:/application
      - ./php-fpm/php-ini-overrides.ini:/usr/local/etc/php/conf.d/infrastructure-overrides.ini        
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
      - php-fpm
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
      - ../application:/application
  mysql:    
    build:
      context: .
      dockerfile: mysql/Dockerfile
    volumes:
      - "./database:/var/lib/mysql"    
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=database
      - MYSQL_USER=user
      - MYSQL_PASSWORD=pwd
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
    environment:
      - PMA_USER=user
      - PMA_PASSWORD=pwd
      - PMA_HOST=mysql
    depends_on:
      - mysql
```

> \> tty 1
```bash
$ docker-compose stop && docker-compose up -d --build
```

> \> tty 1
```bash
$ touch .env && vim .env
```

> \> workshopDocker-phpVigo/docker/infrastructure/.env
```dotenv
# Symfony
SYMFONY_APP_PATH=../application

# MySQL
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=db
MYSQL_USER=user
MYSQL_PASSWORD=pwd

# Local
LOCAL_UID=1000
LOCAL_GID=1000
```

> \> tty 1
```bash
$ vim docker-compose.yml
```

> \> workshopDocker-phpVigo/docker/infrastructure/docker-compose.yml
```yaml
version: "3.5"
services:
  php-fpm:
    build:
      context: .
      dockerfile: php-fpm/Dockerfile
      args:
        - LOCAL_UID=${LOCAL_UID}
        - LOCAL_GID=${LOCAL_GID}      
    depends_on:
      - mysql
    volumes:
      - ${SYMFONY_APP_PATH}:/application
      - ./php-fpm/php-ini-overrides.ini:/usr/local/etc/php/conf.d/infrastructure-overrides.ini        
  webserver:
    image: nginx:alpine
    ports:
      - "8080:80"
    depends_on:
      - php-fpm
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/conf.d/default.conf
      - ${SYMFONY_APP_PATH}:/application
  mysql:    
    build:
      context: .
      dockerfile: mysql/Dockerfile
      args:
        - LOCAL_UID=${LOCAL_UID}
        - LOCAL_GID=${LOCAL_GID}          
    volumes:
      - "./database:/var/lib/mysql"    
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8081:80"
    environment:
      - PMA_USER=${MYSQL_USER}
      - PMA_PASSWORD=${MYSQL_PASSWORD}
      - PMA_HOST=mysql
    depends_on:
      - mysql
```

> \> tty 1
```bash
$ vim php-fpm/Dockerfile
```

> workshopDocker-phpVigo/docker/infrastructure/php-fpm/Dockerfile
```dockerfile
FROM rolandocaldas/php:7.2-dev-mysql

ARG LOCAL_UID
ARG LOCAL_GID
RUN usermod -u ${LOCAL_UID:-33} www-data && groupmod -g ${LOCAL_GID:-33} www-data
```

> \> tty 1
```bash
$ vim mysql/Dockerfile
```

> \> workshopDocker-phpVigo/docker/infrastructure/mysql/Dockerfile
```dockerfile
FROM mysql:5.7

ARG LOCAL_UID
ARG LOCAL_GID
RUN usermod -u ${LOCAL_UID:-999} mysql && groupmod -g ${LOCAL_GID:-999} mysql
```

> \> tty 1
```bash
$ docker-compose stop && docker-compose up -d --build
```

> \> tty 1
```bash
$ cd ../application
$ git clone https://github.com/phpvigo/workshop-symfony4.git ./
```

> \> tty 1
```bash
$ cd ../infrastructure
$ docker-compose exec --user www-data php-fpm composer update
```

> \> tty 1
```bash
$ cd ../application
$ vim .env
```

> \> workshopDocker-phpVigo/docker/application/.env
```dotenv
# This file is a "template" of which env vars need to be defined for your application
# Copy this file to .env file for development, create environment variables when deploying to production
# https://symfony.com/doc/current/best_practices/configuration.html#infrastructure-related-configuration

#Twitter oAuth credentials
TWITTER_CONSUMER_KEY=my_key
TWITTER_CONSUMER_SECRET=my_secret
TWITTER_TOKEN=my_token
TWITTER_TOKEN_SECRET=my_token_secret

###> symfony/framework-bundle ###
APP_ENV=dev
APP_SECRET=92bb31e812cf373edb0da79a7eab92b9
#TRUSTED_PROXIES=127.0.0.1,127.0.0.2
#TRUSTED_HOSTS=localhost,example.com
###< symfony/framework-bundle ###

###> doctrine/doctrine-bundle ###
# Format described at http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/configuration.html#connecting-using-a-url
# For an SQLite database, use: "sqlite:///%kernel.project_dir%/var/data.db"
# Configure your db driver and server_version in config/packages/doctrine.yaml
DATABASE_URL=mysql://user:pwd@mysql:3306/database
###< doctrine/doctrine-bundle ###
```

> \> tty 1
```bash
$ cd ../infrastructure
$ docker-compose exec --user www-data php-fpm php bin/console make:migration
```

> \> tty 1
```bash
$ docker-compose exec --user www-data php-fpm php bin/console doctrine:migrations:migrate
```
