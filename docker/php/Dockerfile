FROM debian:latest
LABEL maintainer="jakob@hexio.dk"

RUN echo "deb http://httpredir.debian.org/debian stretch main\ndeb-src http://httpredir.debian.org/debian stretch main" > /etc/apt/sources.list.d/stretch.list
RUN apt-get clean && apt-get update && apt-get install -my \
  apache2 \
  libapache2-mod-php7.0 \
  php7.0 \
  php7.0-intl \
  php7.0-mysql \
  php7.0-xdebug \
  php7.0-mbstring \
  php7.0-ssh2 \
  composer \
  zip \
  unzip \
  curl \
  php-curl \
  cron \
  fontconfig \
  mysql-client \
  vim \
  php-imagick \
  php-simplexml

RUN rm /etc/apache2/sites-available/000-default.conf
COPY 000-zf3.conf /etc/apache2/sites-available/000-default.conf
RUN mkdir -p /data/www

COPY php.ini  /etc/php/7.0/apache2/conf.d/custom.ini

RUN a2enmod rewrite
RUN a2enmod ssl

RUN mkdir /ssl
RUN openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 \
    -subj "/C=DK/ST=Sjaelland/L=Naestved/O=Hexio/CN=WompWomp" \
    -keyout /ssl/ssl.key -out /ssl/ssl.crt

RUN cd /tmp \
    && curl -o /tmp/mod-pagespeed.deb https://dl-ssl.google.com/dl/linux/direct/mod-pagespeed-beta_current_amd64.deb \
    && dpkg -i /tmp/mod-pagespeed.deb \
    && apt-get -f install

COPY scripts/setupDevDB /usr/bin/setupDevDB
RUN chmod +x /usr/bin/setupDevDB
RUN sed -i 's/\r//' /usr/bin/setupDevDB

COPY scripts/applyUpgrades /usr/bin/applyUpgrades
RUN chmod +x /usr/bin/applyUpgrades
RUN sed -i 's/\r//' /usr/bin/applyUpgrades

WORKDIR "/data/www"

EXPOSE 80
EXPOSE 443

COPY httpd-foreground /usr/bin/
RUN chmod +x /usr/bin/httpd-foreground
RUN sed -i 's/\r//' /usr/bin/httpd-foreground

CMD ["httpd-foreground"]
