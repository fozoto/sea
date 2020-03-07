# SpaceVim的Java模块

## 激活Java模块

> vim ~/.SpaceVim.d/init.toml

```shell
[[layers]]
name = 'lang#java'
```

## 如果代码无法提示了

```shell
cd ~/.cache/vimfiles/repos/github.com/artur-shaik/vim-javacomplete2/libs/javavi
mvn clean compile
```

## 代码提示

ctrl+x与ctrl+o
