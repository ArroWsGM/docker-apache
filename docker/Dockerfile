FROM php:8.0.17-apache

ARG WWWUSER
ARG WWWGROUP
ARG UNAME=www-data
ARG UGROUP=www-data
ARG NODE_VERSION=16

WORKDIR /var/www/html

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update \
    && apt-get install -y gosu curl ca-certificates zip unzip git supervisor libcap2-bin libpng-dev python2 ssl-cert sudo

RUN curl -sSLf -o /usr/local/bin/install-php-extensions https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions \
    && chmod +x /usr/local/bin/install-php-extensions

RUN install-php-extensions gd imap zip bcmath soap intl ldap memcached imagick msgpack igbinary redis xdebug \
    sockets pdo_mysql mysqli gettext calendar exif

RUN php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && curl -sL https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm yarn \
    && apt-get update \
    && apt-get install -y default-mysql-client \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN curl -LJO https://github.com/mailhog/mhsendmail/releases/download/v0.2.0/mhsendmail_linux_amd64 \
    && chmod +x mhsendmail_linux_amd64 \
    && mv mhsendmail_linux_amd64 /usr/local/bin/mhsendmail

RUN a2enmod ssl rewrite
RUN git config --global init.defaultBranch main

EXPOSE 80 443

RUN usermod  --uid $WWWUSER $UNAME
RUN groupmod --gid $WWWGROUP $UGROUP
RUN echo "$UNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN touch /var/www/.yarnrc && mkdir -p /var/www/.cache/yarn
RUN mkdir /var/www/.npm && chown -R $UNAME:$UGROUP /var/www
