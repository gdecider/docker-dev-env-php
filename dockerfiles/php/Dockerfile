FROM php:7.2.10-apache
RUN mkdir /tmp/phpsessions && chown -R www-data:www-data /tmp/phpsessions
RUN apt-get update && apt-get install -y \
        apt-utils
RUN apt-get update && apt-get install -y \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng-dev \
    && docker-php-ext-install -j$(nproc) mysqli pdo_mysql \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) opcache \
    && docker-php-ext-configure opcache --enable-opcache
RUN groupadd -g 1000 -r www \
    && useradd -u 1000 -g 1000 -r www