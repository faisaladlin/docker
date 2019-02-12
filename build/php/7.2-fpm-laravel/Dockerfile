FROM faisaladlin/php:7.2-fpm
LABEL maintainer="faisaladlin@gmail.com"

# With NodeJS & NPM
ARG WITH_NODEJS=true

# ------------------------------------------------------------------------------------------------

# Install npm & nodejs (recommended for server side javascript operations)
RUN [[ "${WITH_NODEJS}" = true ]] && apk add --no-cache npm

# Adds artisan exec / alias for "php artisan" command
COPY ./custom/user-bin/artisan /usr/local/bin/
RUN chmod +x /usr/local/bin/artisan || rm /usr/local/bin/artisan

# Configs directory:    /usr/local/etc/php
# Work directory:       /var/www
# Exposed Port:         9000
