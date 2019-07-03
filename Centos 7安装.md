### 安装nginx
```
// 安装yum源
sudo rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm


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


// 打开防火墙端口(默认 CentOS7 使用的防火墙 firewalld 是关闭 http 服务的（打开 80 端口）。)
sudo firewall-cmd --zone=public --permanent --add-service=http
sudo firewall-cmd --reload
// 打开之后，可以查看一下防火墙打开的所有的服务
sudo sudo firewall-cmd --list-service
ssh dhcpv6-client http 表示系统已经打开了 http 服务。

// 反向代理
// Nginx 是一个很方便的反向代理，配置反向代理可以参考 Module ngx_http_proxy_module 。本文不做累述。

// 需要指出的是 CentOS 7 的 SELinux，使用反向代理需要打开网络访问权限。

sudo setsebool httpd_can_network_connect 1 
// 打开网络权限之后，反向代理可以使用了。

// 绑定其他端口
// Nginx 默认绑定的端口是 http 协议的默认端口，端口号为：80，如果需要绑定其他端口，需要注意 SELinux 的配置

// 例如：绑定 8081 端口，但是会发现无法启动，一般的报错如下

// YYYY/MM/DD hh:mm:ss [emerg] 46123#0: bind() to 0.0.0.0:8081 failed (13: Permission denied)
// 此时需要更改 SELinux 的设置。我们使用 SELinux 的管理工具 semanage 进行操作，比较方便。

// 安装 semanage 使用如下命令

sudo yum install policycoreutils-python
// 然后查看是否有其他协议类型使用了此端口

sudo semanage port -l | grep 8081
transproxy_port_t              tcp      8081
// 返回了结果，表明已经被其他类型占用了，类型为 transproxy_port_t。

// 我们还要查看一下 Nginx 的在 SELinux 中的类型 http_port_t 绑定的端口

sudo semanage port -l | grep http_port_t
http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
pegasus_http_port_t            tcp      5988
// 第一行 http_port_t 中没有包含 8081 这个端口。因此需要修改 8081 端口到 http_port_t 类型中。

sudo semanage port -m -p tcp -t http_port_t 8081
// 如果没有其他协议类型使用想要绑定的端口，如 8001，则我们只要新增到 SELinux 中即可。

sudo semanage port -l | grep 8001
sudo semanage port -a -p tcp -t http_port_t 8001
// 此时，重新启动 Nginx 即可。


```
