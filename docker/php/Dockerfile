# FROM php:8.1-apache
FROM php:7.4-apache

# install dependencies of php extensions
RUN apt-get update
RUN apt-get install -y zip libzip-dev

# install php extensions
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install zip
RUN pecl install xdebug-2.8.1 && docker-php-ext-enable xdebug

# install composer
# NOTE: the commit hash can be updated to update the installer
RUN curl -s https://raw.githubusercontent.com/composer/getcomposer.org/d366ce4e77501dd11a28baace02e702dd9f1c203/web/installer | php -- --install-dir=/bin --filename=composer
RUN mkdir /var/www/.composer && chown www-data:www-data /var/www/.composer

# configure php
COPY docker/php/php/xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
COPY docker/php/php/error_reporting.ini /usr/local/etc/php/conf.d/error_reporting.ini

# configure apache
RUN a2enmod rewrite
RUN a2disconf other-vhosts-access-log
COPY ./docker/php/apache/apache2.conf /etc/apache2/apache2.conf
COPY ./docker/php/apache/ports.conf /etc/apache2/ports.conf
COPY ./docker/php/apache/sites-available/* /etc/apache2/sites-available/
RUN a2ensite 000-default

# add data
COPY ./siteroot /var/www/html/