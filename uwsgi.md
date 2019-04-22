## 直接使用pip 安装uwsgi
安装完成后配置uwsgi.ini文件
```
[uwsgi]
socket = 0.0.0.0:9090
master = true
chdir = /home/flask/
wsgi-file = run.py
callable = app
processes = 4
threads = 4
buffer-size = 32768


chmod-socket=666
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
socket=%(chdir)/uwsgi/uwsgi.sock

# status文件，可以查看uwsgi的运行状态
stats=%(chdir)/uwsgi/uwsgi.status

# pid文件，通过该文件可以控制uwsgi的重启和停止
pidfile=%(chdir)/uwsgi/uwsgi.pid

# 日志文件，通过该文件查看uwsgi的日志
daemonize=%(chdir)/uwsgi/uwsgi.log

```

启动命令 uwsgi  --ini  uwsgi.ini
停止命令 uwsgi --stop uwsgi.pid
重启命令 uwsgi --reload uwsgi.pid
