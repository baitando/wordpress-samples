FROM php:7.2-apache
RUN apt-get update; apt-get install -y zlib1g-dev libcurl4-gnutls-dev libxml2-dev
RUN docker-php-ext-install bcmath mysqli zip dom intl mbstring
RUN a2enmod deflate expires rewrite headers
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"