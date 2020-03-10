# unixODBC安装

[下载](ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.7.tar.gz)

## 依赖

```shell
sqlplus root/qingyan@ORCL
```

## 编译安装

```shell
/soft/unixODBC-2.3.7/configure --prefix=/soft/unixODBC --sysconfdir=/soft/unixODBC/conf
```

## 配置

1. vim /soft/unixODBC/conf/odbc.ini

```shell
[oracle11g]
Driver= oracle
ServerName= ORCL
UserID= root
Password= qingyan
```

2. vim /soft/unixODBC/conf/odbcinst.ini

```shell
[oracle]
Description     = Oracle ODBC driver for Oracle 11g
Driver          = /soft/oracle/instantclient_11_2/odbc/libsqora.so.11.1
```

## 解决bug

会出现以下问题, 但这个文件是存在的, [参考](https://bbs.csdn.net/topics/390566189)

```shell
qingyan@thinkpad:/soft/unixODBC$ isql oracle11g root qingyan -v
[01000][unixODBC][Driver Manager]Can't open lib '/soft/oracle/instantclient_11_2/odbc/libsqora.so.11.1' : file not found
```

解决如下:

```shell
sudo cp /soft/unixODBC/lib/libodbcinst.so /usr/lib/libodbcinst.so.1
```

## 运行

```shell
isql oracle11g root qingyan -v
```
