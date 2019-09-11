### nginx 配置目录
``` /etc/nginx ```

```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```
### flask反向代理
```
server
{
    listen 1992;
    charset utf-8;
    client_max_body_size 75M;

    root /home/app/video;
    location / {
        include uwsgi_params;
        uwsgi_pass 127.0.0.1:9090;
    }
}
```
### vue后台管理
```
server {
        listen       8888;#默认端口是80，如果端口没被占用可以不用修改
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        root        /home/app/video/admin;
        #vue项目的打包后的dist

        location / {
            try_files $uri $uri/ @router;#需要指向下面的@router否则会出现vue的路
由在nginx中刷新出现404
            index  index.html index.htm;
        }
        #对应上面的@router，主要原因是路由的路径资源并不是一个真实的路径，所以无
法找到具体的文件
        #因此需要rewrite到index.html中，然后交给路由在处理请求资源
        location @router {
            rewrite ^.*$ /index.html last;
        }
  }

```
