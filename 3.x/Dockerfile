FROM php:7.3-apache

# based on official image release from docker
MAINTAINER Alexandre Esser <alexandre@esser.fr>

# install the PHP extensions we need
RUN set -ex; \
  \
  apt-get update; \
  apt-get install -y \
    libzip-dev \
    libicu-dev \
    libpng-dev \
    libxml2-dev \
    zlib1g-dev \
  ; \
  rm -rf /var/lib/apt/lists/*; \
  \
  docker-php-ext-install gd mysqli opcache xmlrpc intl soap zip

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
# see https://docs.moodle.org/36/en/OPcache
RUN { \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.max_accelerated_files=10000'; \
    echo 'opcache.revalidate_freq=60'; \
    echo 'opcache.use_cwd=1'; \
    echo 'opcache.validate_timestamps=1'; \
    echo 'opcache.save_comments=1'; \
    echo 'opcache.enable_file_override=0'; \
  } > /usr/local/etc/php/conf.d/opcache-recommended.ini

RUN a2enmod rewrite expires

VOLUME ["/var/www/html", "/var/www/moodledata"]

ENV MOODLE_BRANCH 311
ENV MOODLE_RELEASE 3.11.18
ENV MOODLE_SHA256 31db93742ca4c1b9679070b28bb9f25fb3150d4365dc85a6b04aab0dfafb62a4

RUN set -ex; \
  curl -o moodle.tgz -fSL "https://packaging.moodle.org/stable${MOODLE_BRANCH}/moodle-${MOODLE_RELEASE}.tgz"; \
  echo "$MOODLE_SHA256 moodle.tgz" | sha256sum -c -; \
# upstream tarballs include ./moodle/ so this gives us /usr/src/moodle
  tar -xzf moodle.tgz -C /usr/src/; \
  rm moodle.tgz; \
  chown -R www-data:www-data /usr/src/moodle

COPY docker-entrypoint.sh /usr/local/bin/

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
