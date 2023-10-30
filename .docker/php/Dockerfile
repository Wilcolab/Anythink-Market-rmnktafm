# defined in docker-compose.yml, from docker-env.env
ARG RUNTIME_PHP_IMAGE

# Use the specified image as the base
FROM ${RUNTIME_PHP_IMAGE}

# Update the packages
# Install system packages required for MongoDB extension
# 'mysql-client' so we can log into mysql from the PHP container with the command 'mysql -h mysql -u root -p' where mysql is the service name
# 'iputils-ping' to get the ping command
RUN apt-get update && apt-get install -y libssl-dev wget git unzip default-mysql-client iputils-ping

RUN pecl apt update \
    && apt install libzip-dev -y \
    && docker-php-ext-install zip \
    && rm -rf /var/lib/apt/lists/*

# Required for MySQL to work in PHP
RUN docker-php-ext-install mysqli && \
    docker-php-ext-install pdo_mysql

# Test if already installed and
# install the <latest> mongodb PHP extension
# RUN pecl install mongodb && docker-php-ext-enable mongodb
RUN bash -c '[[ -n "$(pecl list | grep mongodb)" ]]\
 || (pecl install mongodb && docker-php-ext-enable mongodb)'

# Test if already installed and
# install and enable XDEBUG <latest>
# RUN pecl install xdebug && docker-php-ext-enable xdebug
RUN bash -c '[[ -n "$(pecl list | grep xdebug)" ]]\
 || (pecl install xdebug && docker-php-ext-enable xdebug)'

# install Redis PHP driver
RUN pecl install -o -f redis \
&&  rm -rf /tmp/pear \
&&  docker-php-ext-enable redis \
&&  docker-php-ext-enable pdo_mysql

# Task: copy rep's PHP .ini files to be automatically parsed
#
# directory is related to the PHP service context
# dot NOT use ./filename.ext for root files
#         use filename.ext
COPY .docker/php/docker-php.ini /usr/local/etc/php/conf.d/

COPY .docker/php/xdebug.ini /usr/local/etc/php/conf.d/
 
# Get Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/htdoc
