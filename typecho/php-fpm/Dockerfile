FROM php:7.3-fpm-alpine

ENV php_conf /usr/local/etc/php-fpm.conf
ENV fpm_conf /usr/local/etc/php-fpm.d/www.conf
ENV php_vars /usr/local/etc/php/conf.d/docker-vars.ini

ENV TYPECHO_VERSION 1.1-17.10.30

# install the PHP extensions typecho need
RUN apk update
RUN set -e \
    && apk add bash libpng-dev freetype-dev libjpeg-turbo-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/  \
    && docker-php-ext-install -j$(nproc) gd  \
    && docker-php-ext-install mysqli pdo pdo_mysql

# to solve nginx appeared 502 under high concurrency problems
# open multiple main process
RUN set -e \
    # optimize php-fpm config
    && echo "cgi.fix_pathinfo=0" > ${php_vars} \
    && echo "upload_max_filesize = 1024M"  >> ${php_vars} \
    && echo "post_max_size = 1024M"  >> ${php_vars} \
    && echo "variables_order = \"EGPCS\""  >> ${php_vars} \
    && echo "memory_limit = 512M"  >> ${php_vars} \
    && echo "output_buffering =on" >> ${php_vars} \
    && sed -i \
        -e "s/;catch_workers_output\s*=\s*yes/catch_workers_output = yes/g" \
        -e "s/pm.max_children = 5/pm.max_children = 25/g" \
        -e "s/pm.start_servers = 2/pm.start_servers = 10/g" \
        -e "s/pm.min_spare_servers = 1/pm.min_spare_servers = 8/g" \
        -e "s/pm.max_spare_servers = 3/pm.max_spare_servers = 16/g" \
        -e "s/;pm.max_requests = 500/pm.max_requests = 1024/g" \
        -e "s/;listen.mode = 0660/listen.mode = 0666/g" \
        -e "s/^;clear_env = no$/clear_env = no/" \
        ${fpm_conf}

# Download the latest typecho https://github.com/typecho/typecho/releases/download/v1.1-17.10.30-release/1.1.17.10.30.-release.tar.gz
RUN set -e \
    && wget -O /tmp/typecho.tar.gz https://github.com/typecho/typecho/releases/download/v1.1-17.10.30-release/1.1.17.10.30.-release.tar.gz \
    && tar -xf /tmp/typecho.tar.gz -C /tmp \
    && rm -rf /tmp/typecho.tar.gz \
    && chown -R www-data:www-data /tmp/build

VOLUME /var/www/html

COPY docker-entrypoint.sh /usr/local/bin/

WORKDIR "/var/www/html"

ENTRYPOINT ["docker-entrypoint.sh"]