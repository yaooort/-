### 安装nginx
```
// 安装yum源
centos7
sudo rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
centos8
sudo rpm -ivh http://nginx.org/packages/centos/8/x86_64/RPMS/nginx-1.18.0-2.el8.ngx.x86_64.rpm



// 安装nginx
sudo yum install nginx


// 配置nginx 服务


// 1.设置开机启动
$ sudo systemctl enable nginx
// 2.启动服务
$ sudo systemctl start nginx
// 3.停止服务
$ sudo systemctl restart nginx
// 4.重新加载，因为一般重新配置之后，不希望重启服务，这时可以使用重新加载。
$ sudo systemctl reload nginx

```
### 关闭防火墙：

- systemctl stop firewalld.service #停止firewall
- systemctl disable firewalld.service #禁止firewall开机启动

### 查看已经开放的端口：

```
firewall-cmd --list-ports
```

### 开启端口

```
firewall-cmd --zone=public --add-port=80/tcp --permanent
```


### 命令含义：

- –zone #作用域

- –add-port=80/tcp #添加端口，格式为：端口/通讯协议

- –permanent #永久生效，没有此参数重启后失效

### 重启防火墙

1.启动防火墙

systemctl start firewalld.service

2.关闭防火墙

systemctl stop firewalld.service

3.重启防火墙

systemctl restart firewalld.service

4.新增开放一个端口

firewall -cmd --zone=public --add-port=3306/tcp --permanent

-zone  作用域

-permanent 永久生效   没有参数后重启后失效

- 多个端口:firewall-cmd --zone=public --add-port=80-90/tcp --permanent

5.查看端口信息

firewall -cmd --list -all

- 查看开放端口的信息

firewall -cmd --list-ports

- 删除firewall-cmd --zone=public --remove-port=80/tcp --permanent


### 1. 我们先看看现有的 python2在哪里

```
[root@lidan /]# whereis python
python: /usr/bin/python /usr/bin/python2.7 /usr/bin/python.bak /usr/lib/python2.7 /usr/lib64/python2.7 /etc/python /usr/include/python2.7 /usr/share/man/man1/python.1.gz
[root@lidan bin]# ll python*
lrwxrwxrwx. 1 root root    9 5月  27 2016 python2 -> python2.7
-rwxr-xr-x. 1 root root 7136 11月 20 2015 python2.7
lrwxrwxrwx. 1 root root    7 5月  27 2016 python.bak -> python2
```

### 2. 接下来我们要安装编译 Python3的相关包

```
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel
```

这里面有一个包很关键`libffi-devel`，因为只有3.7才会用到这个包，如果不安装这个包的话，在 make 阶段会出现如下的报错：

```
# ModuleNotFoundError: No module named '_ctypes'
```

### 3. 安装pip，因为 CentOs 是没有 pip 的。

```
#运行这个命令添加epel扩展源 
yum -y install epel-release 
#安装pip 
yum install python-pip
```

### 4. 可以用yum 安装 安装一下 wget

```
yum -y install wget
```

### 5. 我们可以下载 python3.7的源码包了

```
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
#解压缩
tar -zxvf Python-3.7.0.tgz

#进入解压后的目录，依次执行下面命令进行手动编译
./configure prefix=/usr/local/python3 
make && make install
```

如果最后没提示出错，就代表正确安装了，在/usr/local/目录下就会有python3目录

### 6. 添加软链接

```
#添加python3的软链接 
ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3.7 
#添加 pip3 的软链接 
ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3.7
#测试是否安装成功了 
python -V
```

### 7. 更改yum配置，因为其要用到python2才能执行，否则会导致yum不能正常使用（不管安装 python3的那个版本，都必须要做的）



```
vi /usr/bin/yum 
把 #! /usr/bin/python 修改为 #! /usr/bin/python2 
vi /usr/libexec/urlgrabber-ext-down 
把 #! /usr/bin/python 修改为 #! /usr/bin/python2
```
### 安装pipenv
```
pip install pipenv==2018.11.26
```
### 遇到错误ERROR: Package 'setuptools' requires a different Python: 2.7.5 not in '>=3.5'
```
pip install --upgrade 'setuptools<45.0.0'
```
### 安装git
```
yum install git
```
# centos7安装mysql8.0版本教程

# 一、下载mysql yum包

官方下载地址:https://dev.mysql.com/downloads/repo/yum/
或者直接使用wget下载

```
wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
```

# 二、安装软件源

```
rpm -Uvh https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
```

# 三、安装mysql

```
yum install mysql-community-server
```

安装过程中如果出现y/n的话就一直y就行了，如果嫌麻烦可以用这个命令来安装

```
yum install -y mysql-community-server
```

# 四、启动&配置

```
#启动
service mysqld start
#查看运行状态
service mysqld status
```

看到绿色的running代表已经启动成功，然后mysql在5.6之后的版本都会默认生成一个默认密码，是root用户的。通过如下命令查看密码

```
grep 'temporary password' /var/log/mysqld.log
```

用粉色框标识出来的便是默认设置的密码



![img](https://upload-images.jianshu.io/upload_images/5433505-3b18e2b9d2fbded9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/757/format/webp)

查看密码.png

# 五、进入mysql

执行完如下命令之后输入默认密码

```
mysql -u root -p
```
修改root密码
```
alter user 'root'@'localhost' identified by '密码';
```
刷新
```
flush privileges;
```


# 六、创建用户和分配权限

由于mysql8新提供了一种密码加密方式caching-sha2-password，且为默认，目前很多客户端都不支持，所以我们在创建新角色用户的时候可以指定其为mysql_native_password，原来的root账户等不去做任何改变(`无形之中增加了安全性有木有`)

```
#创建一个test用户(一般情况下root用户只在本地环境下使用),密码是29dIg;2^，数据库的密码最好设置得连自己都记不住。
CREATE USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY '29dIg;2^';
#授权数据库给用户，并设置所有ip都可以远程连接
#如果只授予用户某些数据库的话就把*.*改成 数据库名称.*  意思就是 数据库.表
#如果只授予用户某些权限的话，比如只能读不能写，就把GRANT ALL改成GRANT SELECT
GRANT ALL ON *.* TO 'test'@'%';
#当然，你也可以撤销授权,用法和授权几乎都是一样的。
REVOKE ALL ON *.* from 'test'@'%'; 
```

最后使设置立即生效

```
flush privileges
```

# 七、总结

官方表示 MySQL 8 要比 MySQL 5.7 快 `2` 倍，还带来了大量的改进和更快的性能！所以我也是第一时间把[我的网站](https://www.plaza4me.com/)的mysql数据库从5.7升级到了8.0版本。
有些大神已经总结了8.0的诸多好处，我就不多赘述了。

# 安装redis

yum -y install redis

- 启动redis
```
service redis start
```
- 停止redis
```
service redis stop
```
- 查看redis运行状态
```
service redis status
```
- 查看redis进程
```
ps -ef | grep redis
```
### 设置redis为开机自动启动
chkconfig redis on

### 进入redis服务
##### 进入本机redis
redis-cli
##### 列出所有key
keys *

## 防火墙开放相应端口
- 开启6379
/sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
- 开启6380
/sbin/iptables -I INPUT -p tcp --dport 6380 -j ACCEPT
- 保存
/etc/rc.d/init.d/iptables save
- centos 7下执行
service iptables save

## 修改redis默认端口和密码
##### 打开配置文件
vi /etc/redis.conf
##### 修改默认端口，查找 port 6379 修改为相应端口即可
##### 修改默认密码，查找 requirepass foobared 将 foobared 修改为你的密码
##### 使用配置文件启动 redis
redis-server /etc/redis.conf &
##### 使用端口登录
redis-cli -h 127.0.0.1 -p 6179
##### 此时再输入命令则会报错
##### 输入刚才输入的密码
auth 111
##### 停止redis
redis-cli -h 127.0.0.1 -p 6179
shutdown
##### 使用进程ID杀死
ps -ef | grep redis
kill -9 XXX

##### 使用redis desktop manager远程连接redis




# clone 代码
进入python 项目目录
pipenv shell
pipenv install

# 拷贝文件
```
scp /Users/yao/Desktop/6-11.zip  root@103.117.132.61:/usr/share/nginx/html/static
```

# 安装Supervisord
pip install supervisor

#### 生成配置文件
echo_supervisord_conf > supervisord.conf
#### 该命令在当前目录下创建了一个文件名为supervisord.conf的配置文件，编辑配置文件
```
[unix_http_server]
file=/tmp/supervisor.sock   ; UNIX socket 文件，supervisorctl 会使用
;chmod=0700                 ; socket 文件的 mode，默认是 0700
;chown=nobody:nogroup       ; socket 文件的 owner，格式： uid:gid
;[inet_http_server]         ; HTTP 服务器，提供 web 管理界面
;port=127.0.0.1:9001        ; Web 管理后台运行的 IP 和端口，如果开放到公网，需要注意安全性
;username=user              ; 登录管理后台的用户名
;password=123               ; 登录管理后台的密码
[supervisord]
logfile=/tmp/supervisord.log ; 日志文件，默认是 $CWD/supervisord.log
logfile_maxbytes=50MB        ; 日志文件大小，超出会 rotate，默认 50MB
logfile_backups=10           ; 日志文件保留备份数量默认 10
loglevel=info                ; 日志级别，默认 info，其它: debug,warn,trace
pidfile=/tmp/supervisord.pid ; pid 文件
nodaemon=false               ; 是否在前台启动，默认是 false，即以 daemon 的方式启动
minfds=1024                  ; 可以打开的文件描述符的最小值，默认 1024
minprocs=200                 ; 可以打开的进程数的最小值，默认 200
; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
[supervisorctl]
serverurl=unix:///tmp/supervisor.sock ; 通过 UNIX socket 连接 supervisord，路径与 unix_http_server 部分的 file 一致
;serverurl=http://127.0.0.1:9001 ; 通过 HTTP 的方式连接 supervisord
; 包含其他的配置文件
[include]
files = relative/directory/*.ini    ; 可以是 *.conf 或 *.ini
```
#### 运行以下命令启动supervisord进程，可测试supervisord是否安装成功并执行
supervisord -c supervisord.conf

#### 查看系统进程中是否多了一个supervisord
ps -aux | grep supervisord

#### 配置Program
```
;program名称，随便写，但不要重复，是program的唯一标识
[program:server]
;指定运行目录
directory = /home/app/filemanage/
;运行目录下执行命令
command = /root/.local/share/virtualenvs/filemanage-dx6nPVOX/bin/gunicorn -c gun.py wsgi:application
;启动设置
;numprocs=1         ;进程数，注意：（celery进程数量,不是work数量，相当于执行了10个command命令，而不是在celery中指定-c 为10）
autostart = true      ;当supervisor启动时,程序将会自动启动
autorestart = true    ;自动重启（当work被kill了之后会重新启动）
stopasgroup = true
killasgroup = true
;运行程序的用户
;user=root
;startsecs=1 ;程序重启时候停留在runing状态的秒数
;startretries=10 ;启动失败时的最多重试次数
;停止信号,默认TERM
;中断:INT (类似于Ctrl+C)(kill -INT pid)，退出后会将写文件或日志(推荐)
;终止:TERM (kill -TERM pid)
;挂起:HUP (kill -HUP pid),注意与Ctrl+Z/kill -stop pid不同
;从容停止:QUIT (kill -QUIT pid)
stopsignal = INT
```
#### 重启supervisord进程：
supervisorctl -c supervisord.conf reload

### supervisord命令行操作
##### 启动supervisord进程
`supervisord -c supervisord.conf`
##### 关闭supervisord进程
`supervisorctl -c supervisord.conf shutdown #注意这里将supervisord进程关闭，但通过supervisord启动的进程没有关闭`
##### 重启supervisord进程
`supervisorctl -c supervisord.conf reload`
##### 查看进程状态
`supervisorctl`
##### 更多supervisorctl命令
```
$ supervisorctl status
$ supervisorctl stop celery_touchscan # celery_touchscan是一个program的名称
$ supervisorctl start celery_touchscan
$ supervisorctl restart celery_touchscan
$ supervisorctl reread
$ supervisorctl update
```
### 二、设置开机启动supervisor

新建开机启动服务

> $ vim /lib/systemd/system/supervisord.service

在supervisord.service中添加以下内容：
```
# supervisord service for systemd (CentOS 7.0+)
# by ET-CS (https://github.com/ET-CS)
[Unit]
Description=Supervisor daemon[Service]
Type=forking
ExecStart=/usr/bin/supervisord
ExecStop=/usr/bin/supervisorctl $OPTIONS shutdown
ExecReload=/usr/bin/supervisorctl $OPTIONS reload
KillMode=process
Restart=on-failure
RestartSec=42s[Install]
WantedBy=multi-user.target
```


修改/etc/supervisord.conf配置文件：

> \#vim /etc/supervisrod.conf
> nodaemon=false 改成true

将服务脚本添加到systemctl自启动服务：

> $ systemctl enable supervisord.service

重启系统测试开机启动。


### nginx启动可以，不能访问
- 错误 nginx connect() to 0.0.0.0:8888 failed (13: Permission denied) while connecting to upstream
```
setsebool -P httpd_can_network_connect 1
```
### nginx无法启动端口
- https://blog.csdn.net/RunSnail2018/article/details/81185138

### nginx 访问文件权限
https://blog.csdn.net/onlysunnyboy/article/details/75270533

## 导出数据库
mysqldump -h 127.0.0.24 -u oort -p database>old.sql

## 导入数据库
mysql>source old.sql;









## nginx根配置文件
```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    use epoll; 
    worker_connections  65535;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    client_max_body_size   5M;

    upstream backend {
	    	#server 172.26.132.30:8888;
		server 127.0.0.1:8888;
		#server 172.26.72.227:8000;
		#server 172.26.72.225:8000;
    }

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```
### nginx 子配置文件
```
# 测试服务
server {
    	listen       80;
    	server_name  www.btbtrip.cn btbtrip.cn;
#    	rewrite ^(.*)$	https://$host$1	permanent;

    	location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    	}
}

server {
	listen       443 ssl;
    	server_name  www.btbtrip.cn btbtrip.cn;
	#指定crt格式的证书文件 
	ssl_certificate      /etc/nginx/ssl/btbtrip.cn_chain.crt; 
	#指定PEM格式的私钥文件
	ssl_certificate_key  /etc/nginx/ssl/btbtrip.cn_key.key;
	#超时时间
	ssl_session_timeout 5m;

#	location / {
#    	   proxy_pass http://backend;
#  	}
	location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    	}
}

```

## 安装Docker-ce
##### yum install -y yum-utils device-mapper-persistent-data lvm2
##### yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo（中央仓库）
##### yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo（阿里仓库）
##### yum install docker-ce-18.03.1.ce
##### systemctl enable docker # 开机自动启动docker
##### systemctl start docker # 启动docker
##### systemctl restart docker # 重启dokcer

##### 删除镜像 docker rmi id
##### 删除容器 docker rm  id


### Docker 访问宿主机器mysql redis 解决

首先设置了 `mysql` 的配置文件，保证 `mysql` 可以被任何 `ip` 访问：



```bash
[mysqld]
bind-address = 0.0.0.0
```

修改完配置文件重启生效。
 但为了安全考虑，防火墙的 `3306` 端口仍然是不开放外网访问的。

容器访问宿主机的地址使用 `eth0` 的地址，即宿主机内网 `ip` 地址。
 运行 `ipconfig` 命令，查看网络的虚拟网桥相关信息。

注意：宿主机会把容器 `ip` 地址段当成外网 `ip`。（当前说明是 `centos7` 环境）


编辑防火墙文件 `/etc/firewalld/zones/public.xml`，添加下面 `docker0` 地址段到配置：



```xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="ssh"/>
  <service name="dhcpv6-client"/>
  <port protocol="tcp" port="80"/>
  <port protocol="tcp" port="443"/>
  <rule family="ipv4">
    <source address="172.17.0.0/20"/>
    <accept/>
  </rule>
</zone>

```

重启防火墙，`docker` 容器即可正常访问宿主机端口。



```bash
service firewalld restart
```

🎨 如果有用到 `docker-compose` 命令，则会自动创建一个名为 `br-"docker network id"` 的虚拟网桥。
 🎨 此时同样需要将虚拟网桥地址段配置到防火墙白名单，才能正常访问，添加配置：



```xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="ssh"/>
  <service name="dhcpv6-client"/>
  <port protocol="tcp" port="80"/>
  <port protocol="tcp" port="443"/>
  <rule family="ipv4">
    <source address="172.17.0.0/20"/>
    <accept/>
  </rule>
</zone>

```
