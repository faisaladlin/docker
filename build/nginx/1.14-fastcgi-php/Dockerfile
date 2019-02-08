FROM nginx:1.14-alpine
LABEL maintainer="faisaladlin@gmail.com"

# Work directory
ARG WORKDIR=/var/www

# Public site directory (may be different from working directory)
ARG SITEDIR=/var/www/public

# Sendfile should be off for development builds
ARG SENDFILE=off

# GZip should be off for development builds
ARG GZIP=off

# Add headers (Same Origin Policy, XSS Protection, No Sniff Type)
ARG HEADER_SAMEORIGIN=true
ARG HEADER_PROTECT_XSS=true
ARG HEADER_NOSNIFF_TYPE=true

# Server name
ENV SERVER_NAME=localhost

# PHP host (upstream container)
ENV PHP_HOST=php

# FastCGI Timeout (default is 60 seconds. May be extended for debugging purposes)
ENV TIMEOUT=60

# Default timezone
ENV TZ=Asia/Kuala_Lumpur

# ------------------------------------------------------------------------------------------------

# Set / create work directory
WORKDIR $WORKDIR

# Create site directory if missing
RUN [[ ! -d $SITEDIR ]] && mkdir -p $SITEDIR

# Copy contents of /usr/share/nginx/html into site directory (if empty)
RUN [[ -z "$(ls -A "$SITEDIR")" ]] && cp -R /usr/share/nginx/html/. $SITEDIR

# Replace base image's nginx default.conf file
COPY ./custom/config/default.conf /etc/nginx/conf.d/default.conf

# Change nginx site root config to point to site directory
RUN sed -i 's|root[ ]*/usr/share/nginx/html;|root '$SITEDIR';|g' /etc/nginx/conf.d/default.conf

# Disable add same origin header if HEADER_SAMEORIGIN flag NOT true
RUN if [ ! "${HEADER_SAMEORIGIN}" = true ]; then sed -i 's| add_header X-Frame-Options| #add_header X-Frame-Options|g' /etc/nginx/conf.d/default.conf; fi

# Disable add same origin header if HEADER_PROTECT_XSS flag NOT true
RUN if [ ! "${HEADER_PROTECT_XSS}" = true ]; then sed -i 's| add_header X-XSS-Protection| #add_header X-XSS-Protection|g' /etc/nginx/conf.d/default.conf; fi

# Disable add same origin header if HEADER_NOSNIFF_TYPE flag NOT true
RUN if [ ! "${HEADER_NOSNIFF_TYPE}" = true ]; then sed -i 's| add_header X-Content-Type-Options| #add_header X-Content-Type-Options|g' /etc/nginx/conf.d/default.conf; fi

# Set sendfile & gzip on/off switch
RUN sed -i 's|#*sendfile[onf ]*;|sendfile '$SENDFILE';|g' /etc/nginx/nginx.conf \
    && sed -i 's|#*gzip[onf ]*;|gzip '$GZIP';|g' /etc/nginx/nginx.conf

# Prepare entrypoint which sets server_name & fastcgi_pass values by env variable
COPY ./custom/user-bin/docker-nginx-entrypoint /usr/local/bin/docker-nginx-entrypoint
RUN chmod +x /usr/local/bin/docker-nginx-entrypoint
ENTRYPOINT ["docker-nginx-entrypoint"]

# Set default container command
CMD ["nginx", "-g", "daemon off;"]

# Configs directory:    /etc/nginx
# Work directory:       /var/www
# Exposed Port:         80
