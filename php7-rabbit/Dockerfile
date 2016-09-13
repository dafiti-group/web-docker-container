FROM debian:jessie

RUN dpkg-divert --local --rename --add /sbin/initctl && ln -sf /bin/true /sbin/initctl

ENV DEBIAN_FRONTEND noninteractive

RUN useradd -u 1000 docker \
    && apt-get update \
    && apt-get install -y --no-install-recommends wget \
    && rm -r /var/lib/apt/lists/*

RUN echo "deb http://packages.dotdeb.org jessie all" > /etc/apt/sources.list.d/dotdeb.list \
    && echo "deb-src http://packages.dotdeb.org jessie all" >> /etc/apt/sources.list.d/dotdeb.list \
    && wget -O - http://www.dotdeb.org/dotdeb.gpg | apt-key add - \
    && echo "deb http://apt.newrelic.com/debian/ newrelic non-free" > /etc/apt/sources.list.d/newrelic.list \
    && wget --no-check-certificate -O - https://download.newrelic.com/548C16BF.gpg | apt-key add -

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        make \
        unzip \
        pkg-config \
        whiptail \
        mlocate \
        net-tools \
        curl \
        git \
        procps \
        re2c \
        libmagickwand-dev \
        imagemagick \
        libmemcached-dev \
        librabbitmq-dev \
        librabbitmq1 \
        snmp \
        nginx \
        supervisor \
        php7.0-cli \
        php7.0-fpm \
        php7.0-mysql \
        php7.0-intl \
        php7.0-recode \
        php7.0-snmp \
        php7.0-curl \
        php7.0-snmp \
        php7.0-dev \
        php7.0-tidy \
        php7.0-gd \
        php7.0-pspell \
    && rm -r /var/lib/apt/lists/*

ADD https://github.com/mkoppanen/imagick/archive/phpseven.zip /imagick-phpseven.zip
RUN unzip imagick-phpseven.zip \
    && cd imagick-phpseven \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && echo "extension=imagick.so" | tee /etc/php/mods-available/imagick.ini \
    && ln -s /etc/php/mods-available/imagick.ini /etc/php/7.0/fpm/conf.d/20-imagick.ini \
    && ln -s /etc/php/mods-available/imagick.ini /etc/php/7.0/cli/conf.d/20-imagick.ini \
    && cd / \
    && rm imagick-phpseven.zip \
    && rm -rf imagick-phpseven

ADD https://github.com/php-memcached-dev/php-memcached/archive/php7.zip /php-memcached-php7.zip
RUN unzip php-memcached-php7.zip \
    && cd php-memcached-php7 \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && echo "extension=memcached.so" | tee /etc/php/mods-available/memcached.ini \
    && ln -s /etc/php/mods-available/memcached.ini /etc/php/7.0/fpm/conf.d/20-memcached.ini \
    && ln -s /etc/php/mods-available/memcached.ini /etc/php/7.0/cli/conf.d/20-memcached.ini \
    && cd / \
    && rm php-memcached-php7.zip \
    && rm -rf php-memcached-php7

ADD https://github.com/phpredis/phpredis/archive/php7.zip /phpredis-php7.zip
RUN unzip phpredis-php7.zip \
    && cd phpredis-php7 \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && echo "extension=redis.so" | tee /etc/php/mods-available/redis.ini \
    && ln -s /etc/php/mods-available/redis.ini /etc/php/7.0/fpm/conf.d/20-redis.ini \
    && ln -s /etc/php/mods-available/redis.ini /etc/php/7.0/cli/conf.d/20-redis.ini \
    && cd / \
    && rm phpredis-php7.zip \
    && rm -rf phpredis-php7

ADD https://github.com/alanxz/rabbitmq-c/archive/master.zip /rabbitmq-c-master.zip
ADD https://github.com/pinepain/php-amqp/archive/master.zip /php-amqp-master.zip
RUN unzip rabbitmq-c-master.zip \
    && cd rabbitmq-c-master \
    && autoreconf -i \
    && ./configure \
    && make \
    && make install \
    && cd / \
    && rm rabbitmq-c-master.zip \
    && rm -rf rabbitmq-c-master \
    && unzip php-amqp-master.zip \
    && cd php-amqp-master \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && echo "extension=amqp.so" | tee /etc/php/mods-available/amqp.ini \
    && ln -s /etc/php/mods-available/amqp.ini /etc/php/7.0/fpm/conf.d/20-amqp.ini \
    && ln -s /etc/php/mods-available/amqp.ini /etc/php/7.0/cli/conf.d/20-amqp.ini \
    && cd / \
    && rm php-amqp-master.zip \
    && rm -rf php-amqp-master

ADD https://github.com/xdebug/xdebug/archive/master.zip /xdebug-master.zip
RUN unzip xdebug-master.zip \
    && cd xdebug-master \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && echo "zend_extension=xdebug.so" | tee /etc/php/mods-available/xdebug.ini \
    && ln -s /etc/php/mods-available/xdebug.ini /etc/php/7.0/fpm/conf.d/10-xdebug.ini \
    && ln -s /etc/php/mods-available/xdebug.ini /etc/php/7.0/cli/conf.d/10-xdebug.ini \
    && cd / \
    && rm xdebug-master.zip \
    && rm -rf xdebug-master

RUN sed -i "s/;date.timezone =/date.timezone = America\/Sao_Paulo/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/;date.timezone =/date.timezone = America\/Sao_Paulo/" /etc/php/7.0/fpm/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php/7.0/fpm/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php/7.0/fpm/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php/7.0/cli/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php/7.0/fpm/php.ini \
    && sed -i -e "s/;daemonize\s*=\s*yes/daemonize = no/g" /etc/php/7.0/fpm/php-fpm.conf \
    && sed -i "s/www-data/docker/g" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/pm.max_children = 5/pm.max_children = 35/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/pm.start_servers = 2/pm.start_servers = 10/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/pm.min_spare_servers = 1/pm.min_spare_servers = 10/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/pm.max_spare_servers = 3/pm.max_spare_servers = 17/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/;pm.max_requests = 500/pm.max_requests = 250/" /etc/php/7.0/fpm/pool.d/www.conf \
    && sed -i "s/www-data;/docker;\\ndaemon off;/g" /etc/nginx/nginx.conf \
    && echo "[supervisord]" | tee /etc/supervisor/conf.d/core.conf \
    && echo "nodaemon=true" | tee -a /etc/supervisor/conf.d/core.conf \
    && echo "[program:nginx]" | tee /etc/supervisor/conf.d/nginx.conf \
    && echo "command=/usr/sbin/nginx" | tee -a /etc/supervisor/conf.d/nginx.conf \
    && echo "autostart=true" | tee -a /etc/supervisor/conf.d/nginx.conf \
    && echo "autorestart=true" | tee -a /etc/supervisor/conf.d/nginx.conf \
    && echo "priority=10" | tee -a /etc/supervisor/conf.d/nginx.conf \
    && echo "user=root" | tee -a /etc/supervisor/conf.d/nginx.conf \
    && echo "stdout_events_enabled=true" | tee -a /etc/supervisor/conf.d/nginx.conf \
    && echo "stderr_events_enabled=true" | tee -a /etc/supervisor/conf.d/nginx.conf \
    && echo "[program:php-fpm]" | tee /etc/supervisor/conf.d/php-fpm.conf \
    && echo "command=/usr/sbin/php-fpm7.0 --nodaemonize --fpm-config=/etc/php/7.0/fpm/php-fpm.conf" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "autostart=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "autorestart=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "user=root" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "stdout_events_enabled=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "stderr_events_enabled=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && mkdir -p /run/php \
    && curl --insecure -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/bin/composer

RUN echo "\n\nxdebug.remote_enable=1" >> /etc/php/mods-available/xdebug.ini \
     && echo "xdebug.remote_handler=dbgp" >> /etc/php/mods-available/xdebug.ini \
     && echo "xdebug.remote_mode=req" >> /etc/php/mods-available/xdebug.ini \
     && echo "xdebug.remote_port=9001" >> /etc/php/mods-available/xdebug.ini \
     && echo "xdebug.remote_autostart=1" >> /etc/php/mods-available/xdebug.ini \
     && echo "xdebug.remote_connect_back=1" >> /etc/php/mods-available/xdebug.ini \
     && echo "\n\nxdebug.var_display_max_depth = -1 " >> /etc/php/mods-available/xdebug.ini \
     && echo "xdebug.var_display_max_children = -1" >> /etc/php/mods-available/xdebug.ini \
     && echo "xdebug.var_display_max_data = -1 " >> /etc/php/mods-available/xdebug.ini \
     && echo "\n\nxdebug.remote_host="`/sbin/ip route|awk '/default/ { print $6 }'` >> /etc/php/mods-available/xdebug.ini

RUN apt-get remove --purge --auto-remove -y \
        unzip \
        wget \
    && apt-get autoremove -y \
    && apt-get autoclean \
    && apt-get clean \
    && rm -rf /tmp/* /var/lib/apt/lists/* /var/tmp/*

EXPOSE 80 443

CMD ["/usr/bin/supervisord", "-n"]
# ENTRYPOINT ["/run.sh"]
