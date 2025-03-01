FROM php:8.1-rc-cli-alpine3.14

LABEL maintainer="Neo Ighodaro"

WORKDIR /var/www/html

ARG WWWUSER

ENV TZ=UTC
ENV ENV="/root/.ashrc"
ENV DEBIAN_FRONTEND noninteractive

COPY .ashrc /root/.ashrc
COPY php.ini /usr/local/etc/php/conf.d/99-sail.ini
COPY start-container /usr/local/bin/start-container
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN set -eux; \
    chmod +x /usr/local/bin/install-php-extensions \
    # Install persistent / runtime deps
    && apk --update add --no-cache tzdata gnupg su-exec zip unzip git supervisor sqlite libcap nodejs npm \
    # Install PHP extensions
    && install-php-extensions pcntl ldap redis intl soap imap pdo_mysql pcov msgpack bcmath igbinary gd zip opcache @composer

# Clean up
RUN rm -rf /usr/share/php /usr/share/php8 /usr/src/* /usr/local/bin/phpdbg \
        /usr/local/bin/install-php-extensions /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    # Miscellany
    && setcap "cap_net_bind_service=+ep" /usr/local/bin/php \
    && adduser --disabled-password --gecos "" -u 1337 -s /bin/sh -G www-data sail \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && chmod +x /usr/local/bin/start-container \
    && echo http://dl-2.alpinelinux.org/alpine/edge/community/ >> /etc/apk/repositories \
    && apk --no-cache add shadow

ENTRYPOINT ["start-container"]
