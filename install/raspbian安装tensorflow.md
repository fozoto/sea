# raspbian安装tensorflow

```shell
conda install h5py
```

## 设置pip源

```shell
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

## 配置conda源

```shell
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/msys2/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/menpo/
conda config --set show_channel_urls yes
```

可以在 vim ~/.condarc看到

## 参考

[anaconda conda 换源](https://blog.csdn.net/jasonzhoujx/article/details/81130109)
