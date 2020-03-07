# SpaceVim的pylint问题

## anaconda的pylint失效

报错no module

主要是setuptools版本过旧的原因

```shell
conda activate qingyan
pip install -U pip wheel setuptools pylint
```

[参考](https://www.jianshu.com/p/429811934979)

## pylint报错no member

```shell
pylint --generate-rcfile > ~/.pylintrc
```

> vim ~/.pylintrc

```shell
generated-members=torch
```
