FROM alpine:3.8
LABEL maintainer="faisaladlin@gmail.com"

# Default timezone
ENV TZ=Asia/Kuala_Lumpur

# Installs beanstalkd & tzdata package
RUN apk add --no-cache beanstalkd tzdata

# Start beanstalkd with data directory: /var/lib/beanstalkd (for docker volume mounting)
ENTRYPOINT [ "beanstalkd", "-b", "/var/lib/beanstalkd" ]

# Exposed Port:         11300
EXPOSE 11300
