ARG PHP_VERSION
FROM php:${PHP_VERSION}-apache

ENV WEB_DOCUMENT_ROOT=/app \
    APACHE_DOCUMENT_ROOT=/app \
    PHP_MEMORY_LIMIT=2G \
    XDEBUG_MODE= \
    XDEBUG_CLIENT_HOST=host.docker.internal \
    XDEBUG_IDEKEY=docker \
    XDEBUG_OUTPUT_DIR=/tmp/xdebug \
    SMTP_HOST=mailhog \
    SMTP_PORT=1025 \
    COMPOSER_CACHE_DIR=/dev/null

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions opcache xdebug-^3 bcmath gd intl pdo_mysql soap xsl zip mysqli redis sockets ftp pcntl @composer-2 && \
    # Xdebug is loaded on demand by the entrypoint (see XDEBUG_MODE)
    rm "$PHP_INI_DIR/conf.d/docker-php-ext-xdebug.ini"

RUN a2enmod rewrite

RUN sed -ri -e 's!/var/www/html!${WEB_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

# Use the default development configuration
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"

COPY common/conf/php/custom.ini common/conf/php/xdebug.ini $PHP_INI_DIR/conf.d/
# The entrypoint runs as the unprivileged user and writes the Xdebug loader here
RUN chown 1000:1000 "$PHP_INI_DIR/conf.d"

COPY common/conf/bin/debug common/conf/bin/sendmail-smtp common/conf/bin/entrypoint /usr/local/bin/
RUN chmod 755 /usr/local/bin/debug /usr/local/bin/sendmail-smtp /usr/local/bin/entrypoint

RUN apt-get update && apt-get install -y \
    vim \
    wget \
    git \
    htop \
    msmtp \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -u 1000 -m -d /home/application -s /bin/bash application
WORKDIR /app
USER 1000

ENTRYPOINT ["entrypoint"]
CMD ["apache2-foreground"]
