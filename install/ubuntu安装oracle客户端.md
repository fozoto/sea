# ubuntu安装oracle客户端

版本: 11.2.0.4.0

## 下载

```shell
https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html
https://download.oracle.com/otn/linux/instantclient/11204/instantclient-basic-linux.x64-11.2.0.4.0.zip
https://download.oracle.com/otn/linux/instantclient/11204/instantclient-sqlplus-linux.x64-11.2.0.4.0.zip
```

## 解压

```shell
unzip instantclient-basic-linux.x64-11.2.0.4.0.zip -d /soft/oracle
unzip instantclient-sqlplus-linux.x64-11.2.0.4.0.zip -d /soft/oracle
mkdir -p /soft/oracle/instantclient_11_2/network/admin
```

## 配置

```shell
vim /soft/oracle/instantclient_11_2/network/admin/tnsnames.ora
```

输入以下内容:

```shell
ZD_BI =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = 555.555.555.555)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = ORCL)
    )
  )
```

## 配置环境变量

```shell
export ORACLE_BASE=/soft/oracle
export ORACLE_HOME=${ORACLE_BASE}/instantclient_11_2
export TNS_ADMIN=$ORACLE_HOME/network/admin
export NLS_LANG=AMERICAN_AMERICA.UTF8
export PATH=$PATH:$ORACLE_HOME
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$ORACLE_HOME
```

```shell
source /etc/profile
```

## 连接oracle

```shell
sqlplus your_username/your_password@555.555.555.555:1521/ORCL
```

## 修复上下左右键无法使用

```shell
sudo apt-get install rlwrap
vim ~/.bashrc
```

输入以下内容:

```shell
alias sqlplus='rlwrap sqlplus'
```

## 参考文档

[Ubuntu14.04搭建Oracle instantClient 11.2.0.4并配置cx_Oracle5.1.2](https://www.cnblogs.com/colben/p/4135466.html)

[关于oracle命令行上下左右键无法使用解决办法](https://blog.csdn.net/weixin_34280237/article/details/92823403)
