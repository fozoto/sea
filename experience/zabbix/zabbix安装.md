# 安装zabbix

## mysql8.0配置

/usr/share/doc/zabbix-server-mysql/create.sql.gz

```shell
mysql> create user zabbix@'%' identified with mysql_native_password by 'qingyan';
mysql> create database zabbix default charset utf8;
mysql> grant all privileges on zabbix.* to zabbix@'%';
mysql> flush privileges;
```

```shell
docker search zabbix
docker pull zabbix/zabbix-server-mysql

docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="192.168.0.102" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="qingyan" \
      -e MYSQL_ROOT_PASSWORD="qingyan" \
      -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
      --link mysql-server:mysql \
      --link zabbix-java-gateway:zabbix-java-gateway \
      -p 10051:10051 \
      -d zabbix/zabbix-server-mysql:latest
```
docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="172.17.0.1" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="qingyan" \
      -p 10051:10051 \
      -d zabbix/zabbix-server-mysql:latest


## 一个包

```
docker run --name zabbix-appliance -t       -p 10051:10051       -p 80:80       -d zabbix/zabbix-appliance:latest
```


docker run -v /data/test/zabbix:/usr/share/doc/zabbix-server-mysql --name zabbix-server-mysql2333 -t \
      -e DB_SERVER_HOST="172.17.0.1" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="qingyan" \
      -e MYSQL_ROOT_PASSWORD="qingyan" \
      -p 10051:10051 \
      -d zabbix/zabbix-server-mysql:latest
