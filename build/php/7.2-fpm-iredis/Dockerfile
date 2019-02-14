FROM faisaladlin/php:7.2-fpm
LABEL maintainer="faisaladlin@gmail.com"

# Temporary phpiredis folder
ARG PHPIREDIS_PATH=/usr/local/etc/php/iredis

# ------------------------------------------------------------------------------------------------

# Install hiredis alpine package (v0.14 for alpine 3.9)
RUN apk add git hiredis-dev

# Git clone faisaladlin/phpiredis (fork of nrk/phpiredis)
RUN git clone https://github.com/faisaladlin/phpiredis.git $PHPIREDIS_PATH

# Make phpiredis PHP extension
RUN cd $PHPIREDIS_PATH \
    && phpize && ./configure \
    && make && make install \
    && cd ~ && rm -rf $PHPIREDIS_PATH

# Enable phpiredis PHP extension
RUN docker-php-ext-enable phpiredis

# Configs directory:    /usr/local/etc/php
# Work directory:       /var/www
# Exposed Port:         9000
