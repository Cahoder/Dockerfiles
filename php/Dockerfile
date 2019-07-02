# This dockerfile uses the php7.2-fpm image
# VERSION 1 - EDITION 1
# Author: docker_user
# Command format: Instruction [arguments / command ] ..

# Base image to use, this nust be set as the first line

FROM php:7.2-fpm
MAINTAINER fandow@docker.com
# add an group and user by root
USER root
RUN groupadd -g 9999 www-group && \
    useradd www -u 999 -g www-group -d /var/www/html
# define php ini path and working dir
ENV PHP_INI_DIR /usr/local/etc/php
ENTRYPOINT ["docker-php-entrypoint"]
WORKDIR /var/www/html
# use the default production configuration
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"

# override with custom opcache settings if you need
#COPY config/opcache.ini $PHP_INI_DIR/conf.d/

# set timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# allow running as an arbitrary user
RUN set -eux; \
	if [ ! -d "$PHP_INI_DIR/conf.d" ]; \
	then mkdir -p "$PHP_INI_DIR/conf.d"; \
	fi; \
	if [ ! -d /var/www/html ]; \
	then mkdir -p /var/www/html; \
	fi; \
	chown www:www-group /var/www/html; \
	chmod g+rwx /var/www/html

# customize to install docker-php-ext-*
RUN apt-get update && apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-install zip \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install opcache \
    && docker-php-ext-install mysqli

# use pecl to install others extension
RUN pecl install redis-4.0.1 && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini \
    && pecl install xdebug-2.6.0 \
    && docker-php-ext-enable redis xdebug

# memcached-3.0.4 requires PHP >= 7.0.0
RUN apt-get install -y libmemcached-dev zlib1g-dev \
    && pecl install memcached-3.0.4 && echo "extension=memcached.so" > /usr/local/etc/php/conf.d/memcached.ini \
    && docker-php-ext-enable memcached

# memcache extension install
RUN set -eux \
    && cd /tmp \
    && curl -sSL -o php7-memcache.tar.gz https://github.com/websupport-sk/pecl-memcache/archive/php7.tar.gz \
    && tar -zxvf php7-memcache.tar.gz \
    && cd pecl-memcache-php7 \
    && /usr/local/bin/phpize \
    && ./configure --with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=memcache.so" > /usr/local/etc/php/conf.d/memcache.ini \
    && rm -rf /tmp/pecl-memcache-php7 php7-memcache.tar.gz

# composer install
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

# clean all the apt-get files
RUN rm -r /var/lib/apt/lists/*

# override stop signal to stop process gracefully
STOPSIGNAL SIGQUIT

EXPOSE 9000
CMD [ "php-fpm" ]
