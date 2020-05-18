# 总览
[English](https://github.com/Nedved-liao/Dockerfile/blob/master/typecho/README.md) | 简体中文

这个repo用于搭建typecho博客

这是一个Dockerfile / image，用于为nginx和php-fpm构建容器
- 支持SSL配置
- 自定义nginx配置
- 自定义php配置
- 优化php基础参数
- 优化nginx基础参数
- nginx配置文件带有反爬虫配置(默认不开启)
- php与nginx通信使用sock


如果您有任何改进或建议，请在GitHub项目页面上打开问题或提出请求。


# 版本
| Docker Tag   | Nginx Version | PHP Version | Alpine Version | note | port |
| ------------ | ------------- | ----------- | -------------- | -------------- | -------------- | 
| nginx-php7.3-fpm | 1.18.0        | 7.3.17       | 3.10           | 集成NGINX | 80, 443 |
| php7.3-fpm | 1.18.0        | 7.3.17       | 3.10           |  | |

# 模块注释
| ation   | Version | PHP Version                                                  |
| :------ | :------ | :----------------------------------------------------------- |
| php-fpm | 7.3.17  | Core<br/>ctype<br/>curl<br/>date<br/>dom<br/>fileinfo<br/>filter<br/>ftp<br/>gd<br/>hash<br/>iconv<br/>json<br/>libxml<br/>mbstring<br/>mysqli<br/>mysqlnd<br/>openssl<br/>pcre<br/>PDO<br/>pdo_mysql<br/>pdo_sqlite<br/>Phar<br/>posix<br/>readline<br/>Reflection<br/>session<br/>SimpleXML<br/>sodium<br/>SPL<br/>sqlite3<br/>standard<br/>tokenizer<br/>xml<br/>xmlreader<br/>xmlwriter<br/>zlib |
| nginx   | 1.18.0  | --prefix=/etc/nginx<br/>--sbin-path=/usr/sbin/nginx<br/>--modules-path=/usr/lib/nginx/modules<br/>--conf-path=/etc/nginx/nginx.conf<br/>--error-log-path=/var/log/nginx/error.log<br/>--http-log-path=/var/log/nginx/access.log<br/>--pid-path=/var/run/nginx.pid<br/>--lock-path=/var/run/nginx.lock<br/>--http-client-body-temp-path=/var/cache/nginx/client_temp<br/>--http-proxy-temp-path=/var/cache/nginx/proxy_temp<br/>--http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp<br/>--http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp<br/>--http-scgi-temp-path=/var/cache/nginx/scgi_temp<br/>--with-perl_modules_path=/usr/lib/perl5/vendor_perl<br/>--user=nginx<br/>--group=nginx<br/>--with-compat<br/>--with-file-aio<br/>--with-threads<br/>--with-http_addition_module<br/>--with-http_auth_request_module<br/>--with-http_dav_module<br/>--with-http_flv_module<br/>--with-http_gunzip_module<br/>--with-http_gzip_static_module<br/>--with-http_mp4_module<br/>--with-http_random_index_module<br/>--with-http_realip_module<br/>--with-http_secure_link_module<br/>--with-http_slice_module<br/>--with-http_ssl_module<br/>--with-http_stub_status_module<br/>--with-http_sub_module<br/>--with-http_v2_module<br/>--with-mail<br/>--with-mail_ssl_module<br/>--with-stream<br/>--with-stream_realip_module<br/>--with-stream_ssl_module<br/>--with-stream_ssl_preread_module<br/>--with-cc-opt='-Os<br/>-fomit-frame-pointer'<br/>--with-ld-opt=-Wl,--as-needed |

# 链接

- https://github.com/Nedved-liao/Dockerfile/tree/master/typecho
- https://hub.docker.com/r/nedvedliao/typecho

# 快速开始


## 从构建开始
```
git init
git remote add typecho https://github.com/Nedved-liao/Dockerfile.git
git fetch typecho
git checkout typecho/master --  typecho
```
### 构建nginx-php-fpm镜像
```
cd typecho/nginx-php-fpm
chmod +x docker-entrypoint.sh
docker build -t typecho:nginx-php7.3-fpm .
```
### 构建php-fpm镜像
```
cd typecho/php-fpm
chmod +x docker-entrypoint.sh
docker build -t typecho:php7.3-fpm .
```

## 从docker hub拉取镜像:

```
sudo docker push nedvedliao/typecho:nginx-php7.3-fpm
sudo docker push nedvedliao/typecho:php7.3-fpm
```

# 启动

使用集成nginx镜像启动基础容器:

```
sudo docker run -d --name typecho -p 80:80 nedvedliao/typecho:nginx-php7.3-fpm 
```

你可以通过浏览器访问http://\<docker_host\>/install.php 进行安装typecho

# 自定义挂载

如果你想自定义网站目录，你可以使用以下方式启动。

```
sudo docker run -d --name typecho -p 80:80 -v /your_code_directory:/var/www/html  nedvedliao/typecho:nginx-php7.3-fpm 
```
<details>
<summary>更多</summary>
<pre><code>docker run --name typecho -p 80:80 \
-v /your_code_directory:/var/www/html \
-v /your_nginx_conf_path:/etc/nginx/nginx.conf \
-v /your_nginx_virtual_host_conf_path:/etc/nginx/conf.d \
-v /your_nginx_certs_path:/etc/nginx/certs \
-d nedvedliao/typecho:nginx-php7.3-fpm
</code></pre>
</details>


# 使用docker-compose快速部署
1.直接使用nginx-php-fpm项目的[docker-compose.yaml](https://github.com/Nedved-liao/Dockerfile/blob/master/typecho/nginx-php-fpm/docker-compose.yaml)去docker-compose up -d即可

2.使用php-fpm项目的[docker-compose.yaml](https://github.com/Nedved-liao/Dockerfile/blob/master/typecho/nginx-php-fpm/docker-compose.yaml)部署,需并把对应的配置文件放进nginx目录再docker-compose up -d
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




# 贡献者
Author：[<img src="https://avatars2.githubusercontent.com/u/35453327?s=460&amp;u=5e51cadc61eb784aeb28040b1811b7357c5a28d8&amp;v=4" width="50" style="max-width:100%;" />](https://github.com/Nedved-liao)

Email: admin@nedved.st

Link: https://www.nedved.st
