FROM debian:wheezy

RUN dpkg-divert --local --rename --add /sbin/initctl && ln -sf /bin/true /sbin/initctl

ENV DEBIAN_FRONTEND noninteractive

RUN useradd -u 1000 docker \
    && apt-get update \
    && apt-get install -y --no-install-recommends wget \
    && rm -r /var/lib/apt/lists/*

RUN echo "deb http://packages.dotdeb.org wheezy all" > /etc/apt/sources.list.d/dotdeb.list \
    && echo "deb-src http://packages.dotdeb.org wheezy all" >> /etc/apt/sources.list.d/dotdeb.list \
    && wget -O - http://www.dotdeb.org/dotdeb.gpg | apt-key add - \
    && echo "deb http://apt.newrelic.com/debian/ newrelic non-free" > /etc/apt/sources.list.d/newrelic.list \
    && wget --no-check-certificate -O - https://download.newrelic.com/548C16BF.gpg | apt-key add -

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        cmake \
        make \
        g++ \
        unzip \
        pkg-config \
        whiptail \
        mlocate \
        net-tools \
        curl \
        procps \
        re2c \
        supervisor \
        nginx \
        php5-cli \
        php5-fpm \
        php5-mysql \
        php5-intl \
        php5-xdebug \
        php5-recode \
        php5-snmp \
        php5-mcrypt \
        php5-memcache \
        php5-memcached \
        php5-imagick \
        php5-curl \
        php5-xsl \
        php5-snmp \
        php5-dev \
        php5-tidy \
        php5-xmlrpc \
        php5-gd \
        php5-pspell \
        php-pear \
        php-apc \
        newrelic-php5 \
    && rm -r /var/lib/apt/lists/*

RUN echo "\n\nxdebug.remote_enable=1" >> /etc/php5/mods-available/xdebug.ini \
    && echo "xdebug.remote_handler=dbgp" >> /etc/php5/mods-available/xdebug.ini \
    && echo "xdebug.remote_mode=req" >> /etc/php5/mods-available/xdebug.ini \
    && echo "xdebug.remote_port=9001" >> /etc/php5/mods-available/xdebug.ini \
    && echo "xdebug.remote_autostart=1" >> /etc/php5/mods-available/xdebug.ini \
    && echo "xdebug.remote_connect_back=1" >> /etc/php5/mods-available/xdebug.ini \
    && echo "\n\nxdebug.var_display_max_depth = -1 " >> /etc/php5/mods-available/xdebug.ini \
    && echo "xdebug.var_display_max_children = -1" >> /etc/php5/mods-available/xdebug.ini \
    && echo "xdebug.var_display_max_data = -1 " >> /etc/php5/mods-available/xdebug.ini \
    && echo "\n\nxdebug.remote_host="`/sbin/ip route|awk '/default/ { print $3 }'` >> /etc/php5/mods-available/xdebug.ini

ADD https://github.com/phpredis/phpredis/archive/2.2.7.zip /phpredis-2.2.7.zip
RUN unzip phpredis-2.2.7.zip \
    && cd phpredis-2.2.7 \
    && phpize \
    && ./configure \
    && make \
    && make install \
    && echo "extension=redis.so" | tee /etc/php5/mods-available/redis.ini \
    && cd /etc/php5/mods-available \
    && php5enmod redis \
    && cd / \
    && rm phpredis-2.2.7.zip \
    && rm -rf phpredis-2.2.7

RUN sed -i "s/;date.timezone =/date.timezone = America\/Sao_Paulo/" /etc/php5/cli/php.ini \
    && sed -i "s/;date.timezone =/date.timezone = America\/Sao_Paulo/" /etc/php5/fpm/php.ini \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php5/cli/php.ini \
    && sed -i "s/short_open_tag = On/short_open_tag = Off/" /etc/php5/fpm/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php5/cli/php.ini \
    && sed -i "s/error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT/error_reporting = E_ALL/" /etc/php5/fpm/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php5/cli/php.ini \
    && sed -i "s/display_errors = Off/display_errors = On/" /etc/php5/fpm/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php5/cli/php.ini \
    && sed -i "s/display_startup_errors = Off/display_startup_errors = On/" /etc/php5/fpm/php.ini \
    && sed -i "s/www-data/docker/g" /etc/php5/fpm/pool.d/www.conf \
    && sed -i "s/pm.max_children = 5/pm.max_children = 35/" /etc/php5/fpm/pool.d/www.conf \
    && sed -i "s/pm.start_servers = 2/pm.start_servers = 10/" /etc/php5/fpm/pool.d/www.conf \
    && sed -i "s/pm.min_spare_servers = 1/pm.min_spare_servers = 10/" /etc/php5/fpm/pool.d/www.conf \
    && sed -i "s/pm.max_spare_servers = 3/pm.max_spare_servers = 17/" /etc/php5/fpm/pool.d/www.conf \
    && sed -i "s/;pm.max_requests = 500/pm.max_requests = 250/" /etc/php5/fpm/pool.d/www.conf \
    && sed -i "s/# server_names_hash_bucket_size 64;/server_names_hash_bucket_size 64;/g" /etc/nginx/nginx.conf \
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
    && echo "command=/usr/sbin/php5-fpm --nodaemonize" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "autostart=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "autorestart=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "user=root" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "stdout_events_enabled=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && echo "stderr_events_enabled=true" | tee -a /etc/supervisor/conf.d/php-fpm.conf \
    && curl --insecure -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/bin/composer

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