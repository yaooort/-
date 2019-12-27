#  centos7 mysql8.0 主从复制搭建.



> - ### 1.主mysql服务更改配置文件修改
>
> - ```shell
>   vi /etc/my.cnf
>   ```
> 
> - 1.1 新增服务唯一ID ，并开启二进制日志。
>
> - ```shell
>   [mysqld]
>   ## 同一局域网内注意要唯一
>   server-id=100  
>   ## 开启二进制日志功能，可以随便取（关键）
>   log-bin=mysql-bin
>   ## 需要同步的数据库
>   binlog_do_db = video
>   ## 不需要同步的数据库
>   binlog_ignore_db = mysql 
>   ```
>
> - 修改完配置之后需要重启
>
>   ```
>   service mysqld restart
>   ```
>
>   
>
> - 重启之后进入mysql创建从服务器用户
>
> - ```mysql
>   mysql -uroot -p 
>   # 创建从用户
>   CREATE USER 'slave'@'%' IDENTIFIED WITH mysql_native_password BY 'ba@12hgGhkk';
>   # 授予用户 slave REPLICATION SLAVE权限和REPLICATION CLIENT权限，用于在主从库之间同步数据。
>   GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';
>   ```
>
> - 查看主sql状态
>
> - ```mysql
>   show master status;
>   # 记录下File和Position的值，并且不进行其他操作以免引起Position的变化。
>   ```



> - ### 2.从服务器配置
>
> - ```shell
>   vi /etc/my.cnf
>   ```
>   
> - ```ini
>   ## 设置server_id,注意要唯一
>   server-id=101  
>   ## 开启二进制日志功能，以备Slave作为其它Slave的Master时使用
>   log-bin=mysql-slave-bin   
>   ## relay_log配置中继日志
>   relay_log=edu-mysql-relay-bin  
>   ```
>   
> - 修改完配置之后需要重启
>
>   ```
>   service mysqld restart
>   ```
>   
> - 重启之后进入mysql
>
> - ```mysql
>   mysql -uroot -p 
>   # 设置主服务器
>   change master to master_host='172.17.0.2', master_user='slave', master_password='ba@12hgGhkk', master_port=3306, master_log_file='mysql-bin.000001', master_log_pos= 2830, >   >   master_connect_retry=30;
>   ```
>   
>   **master_host** ：Master的地址
>   
>   **master_port**：Master的端口号
>   
>   **master_user**：用于数据同步的用户
>   
>   **master_password**：用于同步的用户的密码
>   
>   **master_log_file**：指定 Slave 从哪个日志文件开始复制数据，即上文中提到的 File 字段的值
>   
>   **master_log_pos**：从哪个 Position 开始读，即上文中提到的 Position 字段的值
>   
>   **master_connect_retry**：如果连接失败，重试的时间间隔，单位是秒，默认是60秒

> - 在从mysql中查看主从同步状态：
>
> - ```mysql
>   show slave status \G;
>   ```
>
>   此时的SlaveIORunning 和 SlaveSQLRunning 都是No，因为我们还没有开启主从复制过程。
>
>   开启主从复制：
>
>   ```
>   start slave;
>   ```
>
>   再次查看同步状态：
>
>   ```
>   show slave status \G;
>   ```
>
>   SlaveIORunning 和 SlaveSQLRunning 都是Yes说明主从复制已经开启。

若SlaveIORunning一直是Connecting，有下面4种原因：

1、网络不通，检查ip端口

2、密码不对，检查用于同步的用户名和密码

3、pos不对，检查Master的Position

4、mysql8特有的密码规则问题引起：

```
ALTER USER 'slave'@'%' IDENTIFIED WITH mysql_native_password BY '@#$Rfg345634523rft4fa';
```

将密码规则修改为：mysql_native_password



如果需要指定想要主从同步哪个数据库，可以在master的`my.cnf`添加配置：

```
binlog-do-db：指定mysql的binlog日志记录哪个db
```

或者在slave的`my.cnf`添加配置：

```
replicate-do-db=需要复制的数据库名，如果复制多个数据库，重复设置这个选项即可 
replicate-ignore-db=需要忽略复制的数据库名，如果忽略复制多个数据库，重复设置这个选项即可
```

如果想要同步所有库和表，在从mysql执行：

```mysql
STOP SLAVE SQL_THREAD;
CHANGE REPLICATION FILTER REPLICATE_DO_DB = ();
start SLAVE SQL_THREAD;
```

如果以上步骤出现问题，可以查看日志：

```
/etc/log/mysqld.log
```






