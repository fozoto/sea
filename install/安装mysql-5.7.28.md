# 安装mysql-5.7.28

> 版本: 5.7.28

## 安装依赖

> apt-get install libaio1

## 安装步骤

```shell
tar -zxvf /soft/mysql-5.7.28-linux-glibc2.12-x86_64.tar.gz -C /soft/mysql-5.7.28
mv /soft/mysql-5.7.28-linux-glibc2.12-x86_64 /soft/mysql-5.7.28
mkdir /data/mysql-5.7.28
```

> vim /soft/mysql-5.7.28/my.cnf

my.cnf的内容如下:

```shell
[mysqld]
basedir=/soft/mysql-5.7.28
datadir=/data/mysql-5.7.28
port=3306
log-error=/data/mysql-5.7.28/error.log
```

## 安装

```shell
groupadd mysql
useradd -r -g mysql -s /bin/false mysql
cd /soft/mysql-5.7.28
mkdir mysql-files
chown mysql:mysql mysql-files
chmod 750 mysql-files
bin/mysqld --defaults-file=/soft/mysql-5.7.28/my.cnf --initialize --user=mysql
bin/mysql_ssl_rsa_setup --defaults-file=/soft/mysql-5.7.28/my.cnf
chown -R mysql /data/mysql-5.7.28
```

## 第一次启动mysql

mysql的安装完root密码在: /data/mysql-5.7.28/error.log
> 2019-10-22T06:17:26.646969Z 1 [Note] A temporary password is generated for root@localhost: (l1fg;spzqyX

```shell
bin/mysqld_safe --defaults-file=/soft/mysql-5.7.28/my.cnf --user=mysql
bin/mysql -u root -p
```

### 修改root密码

> alter user root@'localhost' identified by 'qingyan';

## ubuntu开机自启动mysql

修改启动
> vim /soft/mysql-5.7.28/support-files/mysql.server

内容如下:

```shell
basedir=/soft/mysql-5.7.28
datadir=/data/mysql-5.7.28
```

复制
> cp /soft/mysql-5.7.28/support-files/mysql.server /etc/init.d/mysqld-5.7.28

## 开机启动

systemctl准备:
> vim /lib/systemd/system/mysqld-5.7.28.service

内容如下:

```shell
[Unit]
Description=mysql-5.7.28
SourcePath=/etc/init.d/mysqld-5.7.28
Before=shutdown.target

[Service]
User=mysql
Type=forking
ExecStart=/etc/init.d/mysqld-5.7.28 start
ExecStop=/etc/init.d/mysqld-5.7.28 stop


[Install]
WantedBy=multi-user.target
```

设置
> systemctl enable mysqld-5.7.28.service
