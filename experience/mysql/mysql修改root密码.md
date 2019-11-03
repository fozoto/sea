# mysql修改root密码

修改密码的文件

/home/qingyan/hehe

```txt
alter user root@'localhost' identified with mysql_native_password by 'hehe';
```

启动mysql直接修改密码

```shell
/soft/mysql-8.0.12/bin/mysqld --defaults-file=/soft/mysql-8.0.12/my.cnf --init-file=/home/qingyan/hehe --user=mysql
```
