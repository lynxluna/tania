FROM php:rc-fpm-alpine

# Building base php
COPY ./docker/app/bin/* /usr/local/bin/
COPY ./docker/app/conf/php.ini /usr/local/etc/php/conf.d/
COPY ./docker/app/conf/pool.conf /usr/local/etc/php/

ENV BUILD_PACKAGES="build-base git libmemcached-dev openssl-dev autoconf cyrus-sasl-dev libzip-dev pcre-dev"
RUN apk update \
    && apk add --no-cache $BUILD_PACKAGES \
    && curl -sS -o /tmp/icu.tar.gz -L http://download.icu-project.org/files/icu4c/57.1/icu4c-57_1-src.tgz \
    && tar -zxf /tmp/icu.tar.gz -C /tmp && cd /tmp/icu/source \
    && ./configure --prefix=/usr/local \
    && make && make install \
    && docker-php-ext-install intl pdo pdo_mysql \
    && docker-php-ext-enable opcache \
    && docker-php-pecl-install memcached redis zip \
    && curl -sS https://getcomposer.org/installer | php -- \
      --install-dir=/usr/local/bin \
      --filename=composer \
    && echo "phar.readonly = off" > /usr/local/etc/php/conf.d/phar.ini \
    && apk del $BUILD_PACKAGES

EXPOSE 9000

# Building Tania
ARG BUILD_DATE
ARG VERSION
ARG VCS_URL
ARG VCS_REF

LABEL org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.name="Tania Web Application" \
      org.label-schema.description="Build artifact for Tania" \
      org.label-schema.url="http://gettania.org/" \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.vcs-url="https://github.com/Tanibox/tania.git" \
      org.label-schema.vendor="Tania" \
      org.label-schema.version=$VERSION \
      org.label-schema.schema-version="1.0" \
      authors="Mathew Peterson <mathewpeterson@me.com>, Didiet Noor <dnoor@tanibox.com>"

ADD . /var/www

VOLUME /var/www/var/cache
WORKDIR /var/www
