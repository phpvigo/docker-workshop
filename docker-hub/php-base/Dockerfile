FROM php:7.2-fpm

WORKDIR "/application"

RUN apt-get update \
&& apt-get install -y libicu-dev libpng-dev libjpeg-dev libpq-dev  \
&& apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
&& docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr \
&& docker-php-ext-install intl gd mbstring

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer