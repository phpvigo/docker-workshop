FROM phpvigo/docker-workshop:php-base

# install xdebug
RUN pecl install xdebug \
    && docker-php-ext-enable xdebug
	
RUN apt-get update \
    && apt-get -y install unzip zlib1g-dev \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && docker-php-ext-install zip	