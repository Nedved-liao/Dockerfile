# Overview
English | [简体中文](https://github.com/Nedved-liao/Dockerfile/blob/master/typecho/README_CN.md)

The repo to build typecho blog

This is a Dockerfile/image, used to nginx and php-fpm build container
- Support SSL
- Custom nginx configuration
- Custom php configuration
- Tweak php-fpm config
- Tweak nginx config
- Nginx config file with the anti_spider(default no)
- Php and nginx communication using the sock


If you have any improvement or Suggestions, please in making project page on open questions or request.

# Version
| Docker Tag   | Nginx Version | PHP Version | Alpine Version | note | port |
| ------------ | ------------- | ----------- | -------------- | -------------- | -------------- | 
| nginx-php7.3-fpm | 1.18.0        | 7.3.17       | 3.10           | 集成NGINX | 80, 443 |
| php7.3-fpm | 1.18.0        | 7.3.17       | 3.10           |  | |

# Module
| ation   | Version | PHP Version                                                  |
| :------ | :------ | :----------------------------------------------------------- |
| php-fpm | 7.3.17  | Core<br/>ctype<br/>curl<br/>date<br/>dom<br/>fileinfo<br/>filter<br/>ftp<br/>gd<br/>hash<br/>iconv<br/>json<br/>libxml<br/>mbstring<br/>mysqli<br/>mysqlnd<br/>openssl<br/>pcre<br/>PDO<br/>pdo_mysql<br/>pdo_sqlite<br/>Phar<br/>posix<br/>readline<br/>Reflection<br/>session<br/>SimpleXML<br/>sodium<br/>SPL<br/>sqlite3<br/>standard<br/>tokenizer<br/>xml<br/>xmlreader<br/>xmlwriter<br/>zlib |
| nginx   | 1.18.0  | --prefix=/etc/nginx<br/>--sbin-path=/usr/sbin/nginx<br/>--modules-path=/usr/lib/nginx/modules<br/>--conf-path=/etc/nginx/nginx.conf<br/>--error-log-path=/var/log/nginx/error.log<br/>--http-log-path=/var/log/nginx/access.log<br/>--pid-path=/var/run/nginx.pid<br/>--lock-path=/var/run/nginx.lock<br/>--http-client-body-temp-path=/var/cache/nginx/client_temp<br/>--http-proxy-temp-path=/var/cache/nginx/proxy_temp<br/>--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp<br/>--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp<br/>--http-scgi-temp-path=/var/cache/nginx/scgi_temp<br/>--with-perl_modules_path=/usr/lib/perl5/vendor_perl<br/>--user=nginx<br/>--group=nginx<br/>--with-compat<br/>--with-file-aio<br/>--with-threads<br/>--with-http_addition_module<br/>--with-http_auth_request_module<br/>--with-http_dav_module<br/>--with-http_flv_module<br/>--with-http_gunzip_module<br/>--with-http_gzip_static_module<br/>--with-http_mp4_module<br/>--with-http_random_index_module<br/>--with-http_realip_module<br/>--with-http_secure_link_module<br/>--with-http_slice_module<br/>--with-http_ssl_module<br/>--with-http_stub_status_module<br/>--with-http_sub_module<br/>--with-http_v2_module<br/>--with-mail<br/>--with-mail_ssl_module<br/>--with-stream<br/>--with-stream_realip_module<br/>--with-stream_ssl_module<br/>--with-stream_ssl_preread_module<br/>--with-cc-opt='-Os<br/>-fomit-frame-pointer'<br/>--with-ld-opt=-Wl,--as-needed |

# Links

- https://github.com/Nedved-liao/Dockerfile/tree/master/typecho
- https://hub.docker.com/r/nedvedliao/typecho

# Quick Start


## Building
```
git init
git remote add typecho https://github.com/Nedved-liao/Dockerfile.git
git fetch typecho
git checkout typecho/master --  typecho
```
### Building nginx-php-fpm
```
cd typecho/nginx-php-fpm
chmod +x docker-entrypoint.sh
docker build -t typecho:nginx-php7.3-fpm .
```
### Building php-fpm
```
cd typecho/php-fpm
chmod +x docker-entrypoint.sh
docker build -t typecho:php7.3-fpm .
```

## Pull from the docker hub

```
sudo docker push nedvedliao/typecho:nginx-php7.3-fpm
sudo docker push nedvedliao/typecho:php7.3-fpm
```

# Running

use nginx-php-fpm:

```
sudo docker run -d --name typecho -p 80:80 nedvedliao/typecho:nginx-php7.3-fpm 
```

You can use the browser to access http://\ < docker_host \ > / install. Typecho PHP installation

# Custom mount

If you want to custom website directory, you can use the following way.

```
sudo docker run -d --name typecho -p 80:80 -v /your_code_directory:/var/www/html  nedvedliao/typecho:nginx-php7.3-fpm 
```
<details>
<summary>More</summary>
<pre><code>docker run --name typecho -p 80:80 \
-v /your_code_directory:/var/www/html \
-v /your_nginx_conf_path:/etc/nginx/nginx.conf \
-v /your_nginx_virtual_host_conf_path:/etc/nginx/conf.d \
-v /your_nginx_certs_path:/etc/nginx/certs \
-d nedvedliao/typecho:nginx-php7.3-fpm
</code></pre>
</details>


# Use docker-compose
1.Use nginx-php-fpm project [docker-compose.yaml](https://github.com/Nedved-liao/Dockerfile/blob/master/typecho/nginx-php-fpm/docker-compose.yaml) 

run docker-compose up -d

2.Use php-fpm project [docker-compose.yaml](https://github.com/Nedved-liao/Dockerfile/blob/master/typecho/nginx-php-fpm/docker-compose.yaml)

Need and put the configuration file in nginx directory ,then
docker-compose up - d
```
tree ./nginx
.
├── certs
│   ├── nginx.key
│   └── nginx.pem
├── conf.d
│   └── nginx-site-ssl.conf
└── nginx.conf
```




# The author
Author：[![img](https://avatars2.githubusercontent.com/u/35453327?s=460&u=5e51cadc61eb784aeb28040b1811b7357c5a28d8&v=4)](https://github.com/Nedved-liao)

Email: admin@nedved.st

Link: https://www.nedved.st
