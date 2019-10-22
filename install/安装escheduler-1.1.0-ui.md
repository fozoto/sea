# 安装escheduler-1.1.0-ui

> 版本: 1.1.0

## 依赖

前端依赖nginx, 并且对nginx安装目录有权限修改文件

## 安装步骤

```shell
mkdir -p /app/escheduler-1.1.0-ui
tar -zxvf /app/escheduler-1.1.0-ui.tar.gz -C /app/escheduler-1.1.0-ui/
sh /app/escheduler-1.1.0-ui/install-escheduler-ui.sh
```

## 安装日志

```shell
欢迎使用easy scheduler前端部署脚本,目前前端部署脚本仅支持CentOS,Ubuntu
请在 escheduler-ui 目录下执行
linux
请输入nginx代理端口，不输入，则默认8888 :
请输入api server代理ip,必须输入，例如：192.168.xx.xx :127.0.0.1
请输入api server代理端口,不输入，则默认12345 :
=================================================
        1.CentOS6安装
        2.CentOS7安装
        3.Ubuntu安装
        4.退出
=================================================
请输入安装编号(1|2|3|4)：2
请浏览器访问：http://10.29.4.246:8888
```
