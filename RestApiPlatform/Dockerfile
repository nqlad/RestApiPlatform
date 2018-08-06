FROM php:fpm-alpine

ENV APP_ENV=prod \
    DATABASE_URL='' \
    COMPOSER_HOME=/var/run/composer \
    XDEBUG_CONFIG="remote_enable=1 remote_mode=req remote_port=9001 remote_host=172.17.0.1 remote_log=/var/log/xdebug/remote.log" \
    PHP_IDE_CONFIG="serverName=localhost"

RUN set -xe \
    && apk add --no-cache --update --virtual .build-deps \
        $PHPIZE_DEPS \
        icu-dev \
        postgresql-dev \
    && docker-php-ext-install \
        intl \
        pdo_pgsql \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && mkdir -p /var/log/xdebug \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && chmod -R 0777 \
        /var/run/composer \
        /var/log/xdebug \
    && runDeps="$( \
        scanelf --needed --nobanner --format '%n#p' --recursive /usr/local/lib/php/extensions \
            | tr ',' '\n' \
            | sort -u \
            | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
    )" \
    && apk add --no-cache --virtual .php-phpexts-rundeps $runDeps \
    && apk del .build-deps

WORKDIR /app
COPY . /app
