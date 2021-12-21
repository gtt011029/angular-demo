Nginx



配置文件由三部分组成：

1. 全局块
2. events块（其涉及的指令主要影响nginx服务器与用户的网络连接）
3. http块（涉及代理、缓存、日志定义）
   1. http全局块
   2. 多个server块
      1. server全局块
      2. 多个location块（对Nginx服务器接收到的请求字符串，进行响应；地址定向、数据缓存和应答控制等功能都是在这里实现的）





```shell
# 全局块
user  nginx;

# 指定几个工作线程
worker_processes  1;

# 错误日志
error_log  /var/log/nginx/error.log warn;

# pid 存放路径，这个只能在全局块设置
pid        /var/run/nginx.pid;

# event块
events {
    worker_connections  1024;
}

# http块
http {
	# http全局块
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;
	
	# server块在这个里面
    include /etc/nginx/conf.d/*.conf;
}
```



/etc/nginx/conf.d/*.conf 文件内容

（科普：80端口是为http开放的，浏览器默认的端口号都是80端口，因此，一般我们直接输入localhost就可以了，不用加上80）

```shell
# server块
server {
	# server 全局块
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

	# location 块
    location /en-US/ {
        alias   /opt/xyz_studio/en-US/;
        try_files $uri$args $uri$args/ /en-US/index.html;

        add_header Last-Modified $date_gmt;
        add_header Cache-Control 'no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0';
        if_modified_since off;
        expires off;
        etag off;
    }

    location /zh-Hans/ {
        alias   /opt/xyz_studio/zh-Hans/;
        try_files $uri$args $uri$args/ /zh-Hans/index.html;

        add_header Last-Modified $date_gmt;
        add_header Cache-Control 'no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0';
        if_modified_since off;
        expires off;
        etag off;
    }

    set $first_language 'en-US';
    set $language_suffix 'en-US';

    if ($first_language ~* 'zh-Hans') {
        set $language_suffix 'zh-Hans';
    }

    location / {
        rewrite ^/$ http://$server_addr/$language_suffix/ permanent;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

