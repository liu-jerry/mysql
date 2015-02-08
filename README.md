# About this Repo

主要在原库之上解决编码，以及备份问题

### 构建镜像

```
docker build -t oyach/mysql .
```

### 双主运行(master/master)


配置server-id

```
[mysqld]
log-bin=mysql-bin
server-id=1
```

```
docker run --name master-1 -e MYSQL_ROOT_PASSWORD=root -d oyach/mysql

docker run --name master-2 -e MYSQL_ROOT_PASSWORD=root -d oyach/mysql
```

设置备份用户权限

```
docker exec -ti master-2 sh -c 'mysql -uroot -p"$MYSQL_ROOT_PASSWORD"'

GRANT REPLICATION SLAVE ON *.* to 'repl'@'172.17.%.%' identified by 'repl';
```

设置主从关系：
```
docker exec -ti master-2 sh -c 'mysql -uroot -p"$MYSQL_ROOT_PASSWORD"'

 CHANGE MASTER TO MASTER_HOST='172.17.0.18',MASTER_USER='repl',MASTER_PASSWORD='repl',MASTER_PORT=3306, MASTER_CONNECT_RETRY=30,master_log_file='mysql-bin.000001',master_log_pos=208;
```

 启动slave
 ```
 start slave;
 ```

