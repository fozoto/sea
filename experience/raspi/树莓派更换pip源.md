# 树莓派更换pip源

## 设置源

```shell
mkdir ~/.pip
vim ~/.pip/pip.conf
```

```shell
[global]
timeout =6000
index-url =http://pypi.douban.com/simple/
[install]
use-mirrors =true
mirrors =http://pypi.douban.com/simple/
trusted-host =pypi.douban.com
```

## 国内源

```shell
国内源列表
阿里云 http://mirrors.aliyun.com/pypi/simple/
中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
豆瓣 http://pypi.douban.com/simple
Python官方 https://pypi.python.org/simple/
v2ex http://pypi.v2ex.com/simple/
中国科学院 http://pypi.mirrors.opencas.cn/simple/
清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
```

[树莓派更换pip源](https://blog.csdn.net/qq_40584960/article/details/86080904)
