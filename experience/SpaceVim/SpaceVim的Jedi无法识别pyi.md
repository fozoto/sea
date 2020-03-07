# SpaceVim的Jedi无法识别pyi

## 更新SpaveVim至1.3.0

rm -rf  ~/.cache/vimfiles/repos/github.com/

## pyi文件

cp /file/anaconda3/envs/qingyan/lib/python3.7/site-packages/torch/__init__.pyi /file/anaconda3/envs/qingyan/lib/python3.7/site-packages/torch/jedi.py

> vim /file/anaconda3/envs/qingyan/lib/python3.7/site-packages/torch/__init__.py

增加下面这行

```python
if False:
	from . import jedi
```

