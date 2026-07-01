FROM php:8.3-fpm

RUN apt-get update && apt-get install -y \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libicu-dev \
    zip \
    unzip

# Install core PHP extensions
RUN docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd intl fileinfo opcache

# Copy local OPcache configuration
COPY opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# Install Redis extension
RUN pecl install redis && docker-php-ext-enable redis

# Install Xdebug (for Code Coverage)
RUN pecl install xdebug && docker-php-ext-enable xdebug \
    && echo "xdebug.mode=coverage" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html
