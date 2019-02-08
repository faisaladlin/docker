FROM php:7.2-fpm-alpine3.9
LABEL maintainer="faisaladlin@gmail.com"

# Work directory
ARG WORKDIR=/var/www

# Public site directory (may be different from working directory)
ARG SITEDIR=/var/www/public

# With NodeJS & NPM
ARG WITH_NODEJS=true

# With artisan ('php artisan' alias for Laravel php projects)
ARG WITH_ARTISAN=true

# XDebug enable/disable flag
ENV XDEBUG=false

# XDebug listener (debugger/IDE host name/ip)
ENV XDEBUG_REMOTE_HOST=docker.for.mac.localhost

# Default timezone
ENV TZ=Asia/Kuala_Lumpur

# ------------------------------------------------------------------------------------------------

# Set / create work directory
WORKDIR $WORKDIR

# Create missing site directory & remove /var/www/html (if exists, empty & not SITEDIR)
RUN [[ ! -d $SITEDIR ]] && mkdir -p $SITEDIR \
    && if [ ! "$SITEDIR" == "/var/www/html" ]; then if [ -d "/var/www/html" ]; then [[ -z "$(ls -A "/var/www/html")" ]] && rmdir /var/www/html; fi; fi

# Install tzdata, common PHP extensions & it's prerequisites
RUN apk add tzdata libpng-dev libjpeg-turbo-dev freetype-dev \
    && docker-php-ext-install bcmath gd opcache pdo_mysql zip

# Install PHP Composer into user bin
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/local/bin/composer

# Install XDebug PHP extension via PECL
RUN apk add --no-cache $PHPIZE_DEPS \
    && pecl install xdebug-2.6.1 \
    && docker-php-ext-enable xdebug

# Create php.ini from php.ini-development if missing & set PHP default timezone
RUN [[ ! -e /usr/local/etc/php/php.ini ]] && cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini \
    && sed -i 's|;date.timezone =|date.timezone = '$TZ'|g' /usr/local/etc/php/php.ini

# Install npm & nodejs (recommended for server side javascript operations)
RUN [[ "${WITH_NODEJS}" = true ]] && apk add --no-cache npm

# Replace docker-php-entrypoint which on run:
# > alters PHP.ini timezone based on TZ ENV variable
# > enable/disable XDEBUG based on XDEBUG ENV variable
COPY ./custom/user-bin/docker-php-entrypoint /usr/local/bin/
RUN chmod +x /usr/local/bin/docker-php-entrypoint

# Adds artisan exec / alias for "php artisan" command
COPY ./custom/user-bin/artisan /usr/local/bin/
RUN [[ "${WITH_ARTISAN}" = true ]] && chmod +x /usr/local/bin/artisan || rm /usr/local/bin/artisan

# Configs directory:    /usr/local/etc/php
# Work directory:       /var/www
# Exposed Port:         9000
