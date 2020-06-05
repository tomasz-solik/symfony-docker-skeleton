FROM php:7.4-apache

MAINTAINER Tomasz Solik

RUN apt-get update \
	&& apt-get install -y --no-install-recommends vim curl debconf subversion git apt-transport-https apt-utils \
	zlib1g-dev \
    libxml2-dev \
    libzip-dev \
    libpq-dev \
    libcurl4-openssl-dev \
	zip \
	unzip \
	sudo

# php extensions
RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql zip curl

COPY php.ini /usr/local/etc/php/php.ini

# composer
RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
	mv composer.phar /usr/local/bin/composer

# cleaning
RUN rm -rf /etc/apache2/sites-available/* && rm -rf /etc/apache2/sites-enabled/*
RUN service apache2 restart
RUN rm -rf /var/www/*

# conf apache
RUN mkdir /var/www/app
RUN echo "ServerName symfony.local.com" >> /etc/apache2/apache2.conf
COPY vhosts/symfony.conf /etc/apache2/sites-available/symfony.conf
RUN a2enmod rewrite
RUN usermod -u 1000 www-data
RUN a2ensite symfony.conf
RUN service apache2 restart

#user dev
RUN groupadd dev -g 999
RUN useradd dev -g dev -d /home/dev -m
RUN passwd -d dev

RUN echo "dev ALL=(ALL) ALL" > /etc/sudoers
RUN echo "nameserver 8.8.8.8" >> /etc/resolv.conf

WORKDIR /var/www/app
#RUN composer install --no-dev --no-interaction -o

VOLUME /var/www/app/vendor

# EXPOSE 80

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]