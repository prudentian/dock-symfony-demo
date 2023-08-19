ARG ALPINE_VERSION=3.18
ARG NODE_VERSION=20.5.1

FROM node:${NODE_VERSION}-alpine AS node
FROM alpine:${ALPINE_VERSION}

LABEL Maintainer="Solo"
LABEL Description="Lightweight container with Nginx 1.24 & PHP 8.1 based on Alpine Linux."

# Install packages and remove default server definition
RUN apk add --no-cache \
  acl \
  bash \
  curl \
  fcgi \
	file \
  git \
  nginx \
  php82 \
  php82-ctype \
  php82-curl \
  php82-dom \
  php82-exif \
  php82-fpm \
  php82-gd \
  php82-gettext \
  php82-intl \
  php82-mbstring \
  php82-mysqli \
  php82-opcache \
  php82-openssl \
  php82-pdo \
  php82-pdo_sqlite \
  php82-phar \
  php82-session \
  php82-simplexml \
  php82-tokenizer \
  php82-xml \
  php82-xmlreader \
  supervisor \
  zip

# Configure nginx - http
COPY docker/nginx/nginx.conf /etc/nginx/nginx.conf
# Configure nginx - default server
COPY docker/nginx/conf.d /etc/nginx/conf.d/

# Configure nodejs 
COPY --from=node /usr/lib /usr/lib
COPY --from=node /usr/local/lib /usr/local/lib
COPY --from=node /usr/local/include /usr/local/include
COPY --from=node /usr/local/bin /usr/local/bin

RUN node -v
# RUN chown -R nobody.nobody /root/.npm
RUN npm cache clean --force 

# Configure PHP-FPM
COPY docker/php/fpm-pool.conf /etc/php82/php-fpm.d/www.conf
COPY docker/php/php.ini /etc/php82/conf.d/custom.ini

RUN ln -snf /usr/bin/php82 /usr/bin/php

# Configure supervisord
COPY docker/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Configure Symfony
RUN curl -sS https://get.symfony.com/cli/installer | bash
RUN mv /root/.symfony5/bin/symfony /usr/local/bin/symfony

WORKDIR /var/www/symfony

# Install node module dependencies
# use wildcard to ensure both package.json AND package-lock.json are copied
COPY package*.json /var/www/symfony/
ENV NPM_CONFIG_CACHE /var/www/symfony/.npm
# RUN npm install

# Configure Composer
# Install Composer in supervisord
COPY --from=composer:2.5 /usr/bin/composer /usr/bin/composer
RUN chmod +x /usr/bin/composer
ENV COMPOSER_ALLOW_SUPERUSER 1

COPY composer.json /var/www/symfony/

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody.nobody /var/www/symfony /run /var/lib/nginx /var/log/nginx

# Switch to use a non-root user from here on
USER nobody

# Add application
COPY --chown=nobody ./ /var/www/symfony/

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping