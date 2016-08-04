FROM debian:jessie

# Needed for edit files inside container and
# to suppress messages like "debconf: unable to initialize frontend: Dialog"
ENV TERM linux
# Let the conatiner know that there is no tty
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y \
    curl \
    git \
    libldap2-dev \
    libssl-dev \
    nano \
    nginx \
    supervisor \
    wget \
    vim

RUN echo "deb http://packages.dotdeb.org jessie all" > /etc/apt/sources.list.d/dotdeb.list && \
    echo "deb-src http://packages.dotdeb.org jessie all" >> /etc/apt/sources.list.d/dotdeb.list && \
    wget -O - http://www.dotdeb.org/dotdeb.gpg | apt-key add -

RUN apt-get update && apt-get install -y \
    php7.0-bcmath \
    php7.0-cli \
    php7.0-curl \
    php7.0-fpm \
    php7.0-intl \
    php7.0-json \
    php7.0-ldap \
    php7.0-mbstring \
    php7.0-mcrypt \
    php7.0-mysql \
    php7.0-opcache \
    php7.0-sqlite3 \
    php7.0-xdebug \
    php7.0-xml \
    php7.0-zip

    # Install Composer globally
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

    # Install PHPUnit globally
RUN curl -sSLo /usr/local/bin/phpunit https://phar.phpunit.de/phpunit.phar && chmod +x /usr/local/bin/phpunit

COPY ./supervisor.conf /etc/supervisor/conf.d/
COPY ./web.conf /etc/nginx/sites-available/

    # Global configurations
RUN echo "America/Sao_Paulo" > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata  && \
    # www-data user configurations
    mkdir -p /var/www && \
    usermod -u 1000 www-data && \
    chsh -s /bin/bash www-data && \
    chown -R www-data:www-data /var/www/ && \
    chmod -R g+s /var/www && \
    # Nginx configuration
    echo "daemon off;" >> /etc/nginx/nginx.conf && \
    rm /etc/nginx/sites-enabled/default && \
    ln -nfs /etc/nginx/sites-available/web.conf /etc/nginx/sites-enabled/web.conf && \
    # PHP configuration
    mkdir -p /run/php &&\
    sed -i "s|;daemonize = yes|daemonize = no|"                              /etc/php/7.0/fpm/php-fpm.conf && \
    sed -i "s|listen = \/run\/php\/php7.0-fpm.sock|listen = 9000|"           /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "s|;date.timezone =|date.timezone = America\/Sao_Paulo|"          /etc/php/7.0/cli/php.ini && \
    sed -i "s|;date.timezone =|date.timezone = America\/Sao_Paulo|"          /etc/php/7.0/fpm/php.ini && \
    sed -i "s|;always_populate_raw_post_data|always_populate_raw_post_data|" /etc/php/7.0/cli/php.ini && \
    sed -i "s|;always_populate_raw_post_data|always_populate_raw_post_data|" /etc/php/7.0/fpm/php.ini && \
    # Xdebug configuration
    mkdir -p /etc/php/mods-available && \
    echo "zend_extension=xdebug.so"     >> /etc/php/mods-available/xdebug.ini && \
    echo "xdebug.max_nesting_level=250" >> /etc/php/mods-available/xdebug.ini && \
    echo "xdebug.remote_enable=1"       >> /etc/php/mods-available/xdebug.ini && \
    echo "xdebug.remote_handler=dbgp"   >> /etc/php/mods-available/xdebug.ini && \
    echo "xdebug.remote_mode=req"       >> /etc/php/mods-available/xdebug.ini && \
    echo "xdebug.remote_port=9000"      >> /etc/php/mods-available/xdebug.ini && \
    echo "xdebug.remote_autostart=1"    >> /etc/php/mods-available/xdebug.ini && \
    echo "xdebug.remote_connect_back=1" >> /etc/php/mods-available/xdebug.ini && \
    # Enable Xdebug
    ln -nfs /etc/php/mods-available/xdebug.ini /etc/php/7.0/fpm/conf.d/20-xdebug.ini && \
    ln -nfs /etc/php/mods-available/xdebug.ini /etc/php/7.0/cli/conf.d/20-xdebug.ini

WORKDIR /var/www

EXPOSE 80

ENTRYPOINT ["/usr/bin/supervisord"]
