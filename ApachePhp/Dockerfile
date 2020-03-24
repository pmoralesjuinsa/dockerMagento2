FROM php:7.3.16-apache

RUN apt-get update \
  && apt-get install -y \
    zip \
    unzip \
    sudo \
    cron \
    libicu-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng-dev \
    libxslt1-dev \
    libfreetype6-dev \
    libzip-dev

# install extensions
RUN docker-php-ext-configure gd --with-jpeg-dir --with-freetype-dir && \
    docker-php-ext-install \
      bcmath \
      gd \
      intl \
      mbstring \
      pdo \
      pdo_mysql \
      soap \
      xsl \
      zip \
      sockets

RUN  pecl install mcrypt-1.0.2 && docker-php-ext-enable mcrypt

#Usuario en docker para los permisos
RUN useradd -G www-data,root -u 1000 -d /home/devuser devuser
RUN mkdir -p /home/devuser/.composer && \
    chown -R devuser:devuser /home/devuser

#COMPOSER
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');" && \
    mv composer.phar /usr/local/bin/composer

#XDEBUG
RUN pecl install xdebug
#RUN docker-php-ext-enable xdebug
RUN echo 'xdebug.remote_port=9000' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo 'xdebug.remote_enable=1' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo 'xdebug.remote_connect_back=1' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo 'xdebug.max_nesting_level=-1' >> /usr/local/etc/php/php.ini
RUN echo 'xdebug.profiler_enable_trigger=1' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo 'xdebug.profiler_output_dir=/var/www/html' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo 'xdebug.idekey = PHPSTORM' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo 'xdebug.remote_autostart=1' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

WORKDIR /var/www/html

# creating php.ini file
RUN echo 'memory_limit = 4G\n\
max_execution_time = 1800\n\
zlib.output_compression = On\n\
max_input_vars = 125000' >> /usr/local/etc/php/php.ini

RUN a2enmod rewrite
RUN service apache2 restart

#Add devuser to root group
RUN usermod -a -G root,sudo,devuser devuser
RUN echo "devuser ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers

USER devuser

CMD sudo apache2-foreground