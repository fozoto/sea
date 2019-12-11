# raspbian源码安装pytorch

## 下载源码

```shell
git clone -b v1.3.1 --recursive https://github.com/pytorch/pytorch
cd pytorch
# if you are updating an existing checkout
git submodule sync
git submodule update --init --recursive
```

## 源码安装torch

将torch安装在conda的目录下

```shell
source /soft/berryconda3/envs/pytorch/bin/activate
pip install pyyaml
cd /soft/pytorch
git submodule update --remote third_party/protobuf
export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"}
python setup.py install
pip3 install torchvision
```

## conda安装torchvision

```shell
conda install pillow
pip install torchvision
```

## 也可以这样安装

```shell
python3 setup.py bdist_wheel
sudo pip3 install /soft/pytorch/dist/torch-1.3.0a0+ee77ccb-cp36-cp36m-linux_armv7l.whl
sudo pip3 install torchvision
```

## 问题

[protobuf有bug](https://github.com/pytorch/pytorch/issues/22564)

```shell
make[2]: *** [third_party/protobuf/cmake/CMakeFiles/protoc.dir/build.make:87: bin/protoc] Error 1
make[1]: *** [CMakeFiles/Makefile2:241: third_party/protobuf/cmake/CMakeFiles/protoc.dir/all] Error 2
make: *** [Makefile:141: all] Error 2
```

解决如下:

```shell
cd /soft/pytorch
git submodule update --remote third_party/protobuf
```
