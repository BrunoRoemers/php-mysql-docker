# source: https://github.com/docker-library/php/blob/21967e6cd5f1240093d4f0b03d579397571cab9c/8.2/bullseye/apache/Dockerfile
FROM php:8.2-apache

# install dependencies of php extensions
RUN apt-get update && apt-get install -y zip libzip-dev

# install php extensions
RUN docker-php-ext-install mysqli
RUN docker-php-ext-install zip
RUN pecl install xdebug && docker-php-ext-enable xdebug

# install composer
# NOTE: the commit hash can be updated to update the installer
RUN curl -s https://raw.githubusercontent.com/composer/getcomposer.org/1cf9cac9f0bed289429548be2858dbdfde448da6/web/installer | php -- --install-dir=/bin --filename=composer
RUN mkdir /var/www/.composer && chown www-data:www-data /var/www/.composer

# install supervisor
RUN apt-get update && apt-get install -y supervisor
COPY docker/php/supervisor/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# install ssh # TODO make this the same group as apache
RUN apt-get update && apt-get install -y openssh-server
RUN groupadd sshgroup && useradd -ms /bin/bash -g sshgroup sshuser
RUN mkdir -p /home/sshuser/.ssh
COPY docker/php/ssh/sshd_config /etc/ssh/sshd_config
COPY docker/php/ssh/authorized_keys /home/sshuser/.ssh/authorized_keys
RUN chown sshuser:sshgroup /home/sshuser/.ssh/authorized_keys && chmod 600 /home/sshuser/.ssh/authorized_keys
RUN service ssh start

# install mysql client (mariadb)
RUN apt-get update && apt-get install -y default-mysql-client

# install vim
RUN apt-get update && apt-get install -y vim

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

# run processes in parallel, see supervisord.conf
# docs: https://docs.docker.com/config/containers/multi-service_container/
# docs: http://supervisord.org/running.html
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]