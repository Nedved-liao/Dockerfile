FROM php:7.3-fpm-alpine

ENV php_conf /usr/local/etc/php-fpm.conf
ENV fpm_conf /usr/local/etc/php-fpm.d/www.conf
ENV fpm_conf1 /usr/local/etc/php-fpm.d/www1.conf
ENV fpm_conf2 /usr/local/etc/php-fpm.d/www2.conf
ENV php_vars /usr/local/etc/php/conf.d/docker-vars.ini

ENV NGINX_VERSION 1.18.0
ENV NJS_VERSION   0.4.0
ENV PKG_RELEASE   1

ENV TYPECHO_VERSION 1.1-17.10.30

# install the PHP extensions typecho need and supervisor
RUN apk update
RUN set -e \
    && apk add libpng-dev freetype-dev libjpeg-turbo-dev supervisor \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/  \
    && docker-php-ext-install -j$(nproc) gd  \
    && docker-php-ext-install mysqli pdo pdo_mysql

# begin nginx
# install the nginx source https://github.com/nginxinc/docker-nginx/blob/master/stable/alpine/Dockerfile
RUN set -x \
# create nginx user/group first, to be consistent throughout docker variants
    && addgroup -g 101 -S nginx \
    && adduser -S -D -H -u 101 -h /var/cache/nginx -s /sbin/nologin -G nginx -g nginx nginx \
    && apkArch="$(cat /etc/apk/arch)" \
    && nginxPackages=" \
        nginx=${NGINX_VERSION}-r${PKG_RELEASE} \
        nginx-module-xslt=${NGINX_VERSION}-r${PKG_RELEASE} \
        nginx-module-geoip=${NGINX_VERSION}-r${PKG_RELEASE} \
        nginx-module-image-filter=${NGINX_VERSION}-r${PKG_RELEASE} \
        nginx-module-njs=${NGINX_VERSION}.${NJS_VERSION}-r${PKG_RELEASE} \
    " \
    && case "$apkArch" in \
        x86_64) \
# arches officially built by upstream
            set -x \
            && KEY_SHA512="e7fa8303923d9b95db37a77ad46c68fd4755ff935d0a534d26eba83de193c76166c68bfe7f65471bf8881004ef4aa6df3e34689c305662750c0172fca5d8552a *stdin" \
            && apk add --no-cache --virtual .cert-deps \
                openssl \
            && wget -O /tmp/nginx_signing.rsa.pub https://nginx.org/keys/nginx_signing.rsa.pub \
            && if [ "$(openssl rsa -pubin -in /tmp/nginx_signing.rsa.pub -text -noout | openssl sha512 -r)" = "$KEY_SHA512" ]; then \
                echo "key verification succeeded!"; \
                mv /tmp/nginx_signing.rsa.pub /etc/apk/keys/; \
            else \
                echo "key verification failed!"; \
                exit 1; \
            fi \
            && apk del .cert-deps \
            && apk add -X "https://nginx.org/packages/alpine/v$(egrep -o '^[0-9]+\.[0-9]+' /etc/alpine-release)/main" --no-cache $nginxPackages \
            ;; \
        *) \
# we're on an architecture upstream doesn't officially build for
# let's build binaries from the published packaging sources
            set -x \
            && tempDir="$(mktemp -d)" \
            && chown nobody:nobody $tempDir \
            && apk add --no-cache --virtual .build-deps \
                gcc \
                libc-dev \
                make \
                openssl-dev \
                pcre-dev \
                zlib-dev \
                linux-headers \
                libxslt-dev \
                gd-dev \
                geoip-dev \
                perl-dev \
                libedit-dev \
                mercurial \
                bash \
                alpine-sdk \
                findutils \
            && su nobody -s /bin/sh -c " \
                export HOME=${tempDir} \
                && cd ${tempDir} \
                && hg clone https://hg.nginx.org/pkg-oss \
                && cd pkg-oss \
                && hg up -r 474 \
                && cd alpine \
                && make all \
                && apk index -o ${tempDir}/packages/alpine/${apkArch}/APKINDEX.tar.gz ${tempDir}/packages/alpine/${apkArch}/*.apk \
                && abuild-sign -k ${tempDir}/.abuild/abuild-key.rsa ${tempDir}/packages/alpine/${apkArch}/APKINDEX.tar.gz \
                " \
            && cp ${tempDir}/.abuild/abuild-key.rsa.pub /etc/apk/keys/ \
            && apk del .build-deps \
            && apk add -X ${tempDir}/packages/alpine/ --no-cache $nginxPackages \
            ;; \
    esac \
# if we have leftovers from building, let's purge them (including extra, unnecessary build deps)
    && if [ -n "$tempDir" ]; then rm -rf "$tempDir"; fi \
    && if [ -n "/etc/apk/keys/abuild-key.rsa.pub" ]; then rm -f /etc/apk/keys/abuild-key.rsa.pub; fi \
    && if [ -n "/etc/apk/keys/nginx_signing.rsa.pub" ]; then rm -f /etc/apk/keys/nginx_signing.rsa.pub; fi \
# Bring in gettext so we can get `envsubst`, then throw
# the rest away. To do this, we need to install `gettext`
# then move `envsubst` out of the way so `gettext` can
# be deleted completely, then move `envsubst` back.
    && apk add --no-cache --virtual .gettext gettext \
    && mv /usr/bin/envsubst /tmp/ \
    \
    && runDeps="$( \
        scanelf --needed --nobanner /tmp/envsubst \
            | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
            | sort -u \
            | xargs -r apk info --installed \
            | sort -u \
    )" \
    && apk add --no-cache $runDeps \
    && apk del .gettext \
    && mv /tmp/envsubst /usr/local/bin/ \
# Bring in tzdata so users could set the timezones through the environment
# variables
    && apk add --no-cache tzdata \
# Bring in curl and ca-certificates to make registering on DNS SD easier
    && apk add --no-cache curl ca-certificates \
# forward request and error logs to docker log collector
    && ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log \
# remove default server 
    && rm -f /etc/nginx/conf.d/default.conf
# end nginx

# to solve nginx appeared 502 under high concurrency problems
# open multiple main process
RUN set -e \
    && cp ${fpm_conf} ${fpm_conf1} \   
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
        -e "s/user = www-data/user = nginx/g" \
        -e "s/group = www-data/group = nginx/g" \
        -e "s/;listen.mode = 0660/listen.mode = 0666/g" \
        -e "s/;listen.owner = www-data/listen.owner = nginx/g" \
        -e "s/;listen.group = www-data/listen.group = nginx/g" \
        -e "s/listen = 127.0.0.1:9000/listen = \/var\/run\/php-fpm1.sock/g" \
        -e "s/^;clear_env = no$/clear_env = no/" \
        ${fpm_conf1} \
    && cp ${fpm_conf1} ${fpm_conf2} \
    && sed -i "s/php-fpm1.sock/php-fpm2.sock/" ${fpm_conf2}

# Download the latest typecho https://github.com/typecho/typecho/releases/download/v1.1-17.10.30-release/1.1.17.10.30.-release.tar.gz
RUN set -e \
    && wget -O /tmp/typecho.tar.gz https://github.com/typecho/typecho/releases/download/v1.1-17.10.30-release/1.1.17.10.30.-release.tar.gz \
    && tar -xf /tmp/typecho.tar.gz -C /tmp \
    && rm -rf /tmp/typecho.tar.gz \
    && chown -R nginx:nginx /tmp/build

VOLUME /var/www/html

# add supervisord conf
ADD conf/supervisord.conf /etc/supervisord.conf
# add nginx conf and site(defualt http) conf
ADD conf/nginx.conf /etc/nginx/nginx.conf
ADD conf/nginx-site.conf /etc/nginx/conf.d/nginx-site.conf
COPY docker-entrypoint.sh /usr/local/bin/

EXPOSE 443 80
WORKDIR "/var/www/html"
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["supervisord", "-c", "/etc/supervisord.conf"]