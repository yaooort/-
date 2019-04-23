## 直接使用pip 安装uwsgi
安装完成后配置uwsgi.ini文件
```ini
[uwsgi]
# 暴露给nginx代理的端口及ip
socket=0.0.0.0:9090
# 启动主进程，来管理其他进程，其它的uwsgi进程都是这个master进程的子进程，如果kill这个master进程，相当于重启所有的uwsgi进程。
master=true
# 在app加载前切换到当前目录， 指定运行目录
chdir=/home/app/
# 启动uwsgi的用户名和用户组
uid=root
gid=root
#指定虚拟环境路径
home=/root/.local/share/virtualenvs/app-4uwEKS64
# 指定加载wsgi文件 app入口
wsgi-file=app.py
# uWSGI加载的模块中哪个变量将被调用
callable=app
# 设置工作进程的数量
processes=1
# 设置每个工作进程的线程数
threads=4
# 序列化接受的内容，如果可能的话
thunder-lock=true
# 设置缓冲
post-buffering=4096
# 缓冲区
# buffer-size = 32768
#允许用内嵌的语言启动线程。这将允许你在app程序中产生一个子线程
enable-threads = true
#设置在平滑的重启（直到接收到的请求处理完才重启）一个工作子进程中，等待这个工作>结束的最长秒数。这个配置会使在平滑地重启工作子进程中，如果工作进程结束时间超过了
#8秒就会被强行结束（忽略之前已经接收到的请求而直接结束）
reload-mercy = 8
#为每个工作进程设置请求数的上限。当一个工作进程处理的请求数达到这个值，那么该工>作进程就会被回收重用（重启）。你可以使用这个选项来默默地对抗内存泄漏
max-requests = 5000
#通过使用POSIX/UNIX的setrlimit()函数来限制每个uWSGI进程的虚拟内存使用数。这个配>置会限制uWSGI的进程占用虚拟内存不超过256M。如果虚拟内存已经达到256M，并继续申请>虚拟内存则会使程序报内存错误，本次的http请求将返回500错误。
limit-as = 256
#一个请求花费的时间超过了这个harakiri超时时间，那么这个请求都会被丢弃，并且当前>处理这个请求的工作进程会被回收再利用（即重启）
harakiri = 60
# sock文件权限
chmod-socket=666
# log日志权限
logfile-chmod=644
# uwsgi的进程名称前缀
procname-prefix-spaced=mysite

# py文件修改，自动加载
# py-autoreload=1

# 监听端口，测试时候使用
# http=0.0.0.0:8080

# 退出uwsgi是否清理中间文件，包含pid、sock和status文件
vacuum=true

# socket文件，配置nginx时候使用
socket=%(chdir)/uwsgi.sock

# status文件，可以查看uwsgi的运行状态
stats=%(chdir)/uwsgi.status

# pid文件，通过该文件可以控制uwsgi的重启和停止
pidfile=%(chdir)/uwsgi.pid


# 日志文件，通过该文件查看uwsgi的日志
daemonize=%(chdir)/uwsgi.log

```

启动命令 uwsgi  --ini  uwsgi.ini
停止命令 uwsgi --stop uwsgi.pid
重启命令 uwsgi --reload uwsgi.pid
