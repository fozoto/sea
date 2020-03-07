# go语言连接oracle11g

## 下载basic和sdk

```shell
https://download.oracle.com/otn/linux/instantclient/11204/instantclient-sdk-linux.x64-11.2.0.4.0.zip
https://download.oracle.com/otn/linux/instantclient/11204/instantclient-basic-linux.x64-11.2.0.4.0.zip
https://download.oracle.com/otn/linux/instantclient/11204/instantclient-basiclite-linux.x64-11.2.0.4.0.zip
```



basic的内容解压在/soft/oracle/instantclient_11_2  

sdk的内容解压在/soft/oracle/instantclient_11_2/include

basiclite的内容解压在/soft/oracle/instantclient_11_2/lib64



>  vim /usr/lib/pkgconfig/oci8.pc

```shell
prefix=/soft/oracle/instantclient_11_2
exec_prefix=${prefix}
libdir=${prefix}/lib64
includedir=${prefix}/include

glib_genmarshal=glib-genmarshal
gobject_query=gobject-query
glib_mkenums=glib-mkenums

Name: oci8
Description: oci8 library
Libs: -L${libdir} -lclntsh
Cflags: -I${includedir}
Version: 11.2
```



设置环境变量

> vim /etc/profile

```shell
export PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/lib/pkgconfig
```

