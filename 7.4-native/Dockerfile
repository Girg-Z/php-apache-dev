FROM php:7.4-apache

ENV WEB_DOCUMENT_ROOT /app
ENV APACHE_DOCUMENT_ROOT /app
ENV php.realpath_cache_size 10M
ENV php.realpath_cache_ttl 7200
ENV XDEBUG_CLIENT_HOST 127.0.0.1
ENV XDEBUG_IDEKEY docker

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions xdebug-^3.1 bcmath gd intl pdo_mysql soap xsl zip mysqli redis sockets @composer-2

RUN a2enmod rewrite

RUN sed -ri -e 's!/var/www/html!${WEB_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

# Use the default development configuration
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

COPY common/conf/php/custom.ini $PHP_INI_DIR/conf.d
COPY common/conf/php/xdebug.ini $PHP_INI_DIR/conf.d

COPY common/conf/bin/debug /usr/local/bin
RUN chmod 755 /usr/local/bin/debug

RUN apt-get update && apt-get install -y \
    vim \
    git \
    htop \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -u 1000 application
WORKDIR /app
USER 1000