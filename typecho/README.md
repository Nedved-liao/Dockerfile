# 总览

这个repo用于搭建typecho博客

这是一个Dockerfile / image，用于为nginx和php-fpm构建容器

支持让SSL配置

自定义nginx配置

自定义php配置

如果您有任何改进或建议，请在GitHub项目页面上打开问题或提出请求。

# 版本
| Docker Tag   | Git Release   | Nginx Version | PHP Version | Alpine Version |
| ------------ | ------------- | ------------- | ----------- | -------------- |
| latest/nginx-php7.3-fpm | Master Branch | 1.18.0        | 7.3.17       | 3.10           |

### 链接

- https://github.com/Nedved-liao/Dockerfile/typecho
- https://hub.docker.com/r/nedvedliao/typecho

## 快速开始

To pull from docker hub:

```
docker push nedvedliao/typecho:latest
```

### 启动

To simply run the container:

```
sudo docker run -d nedvedliao/typecho:latest
```

You can then browse to `http://` to view the default install files. To find your `DOCKER_HOST` use the `docker inspect` to get the IP address (normally 172.17.0.2)

For more detailed examples and explanations please refer to the documentation.