FROM php:8.0-apache

ENV PS1='\[\033[1;32m\]🐳  \[\033[1;36m\][\u@\h] \[\033[1;34m\]\w\[\033[0;35m\] \[\033[1;36m\]# \[\033[0m\]'

RUN apt-get update

# Install tools required for build stage
RUN apt-get install -fyqq \
    bash curl wget rsync ca-certificates openssl ssh git tzdata openntpd \
    libxrender1 fontconfig libc6 \
    mariadb-client gnupg binutils-gold autoconf \
    g++ gcc gnupg libgcc1 linux-headers-amd64 make

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin --filename=composer \
 && chmod 755 /usr/bin/composer

## Install additional PHP libraries
RUN docker-php-ext-install bcmath pdo_mysql
#
## Install libraries for compiling GD, then build it


RUN apt-get update && apt-get install -y \
    libjpeg-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libxml2-dev

RUN docker-php-ext-configure gd --with-jpeg && \
    docker-php-ext-install -j$(nproc) gd soap

RUN a2enmod rewrite

# Add ZIP archives support
RUN apt-get install -fyqq zip libzip-dev \
 && docker-php-ext-install zip \
 && apt-get remove -fyqq libzip-dev

# Install xdebug
RUN pecl install xdebug \
 && docker-php-ext-enable xdebug

# Enable XDebug
ADD php/conf/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

RUN useradd -G www-data,root -u 1000 -d /home/devuser devuser
RUN mkdir -p /home/devuser/.composer && \
    chown -R devuser:devuser /home/devuser && \
    chown -R devuser /home/devuser/.composer/

WORKDIR /app

USER devuser