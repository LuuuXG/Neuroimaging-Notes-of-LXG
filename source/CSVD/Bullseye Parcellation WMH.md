# Bullseye Parcellation WMH使用过程记录

## 前言

参考：

- 文档说明：[bullseye parcellation of the cerebral white matter with FreeSurfer](https://gsanroma.github.io/posts/2019/06/bullseye-parcellation/)
- 代码：[bullseye pipeline](https://github.com/gsanroma/bullseye_pipeline)

---

## 准备工作

可能需要修改的地方（Ubuntu 22.04 + Freesurfer 7）：

**注意：需要调用Freesurfer的文件夹，所以要和Freesurfer安装在一个环境**

- 安装时候的问题（执行`python setup.py install`）时：[Problem Installing](https://github.com/gsanroma/bullseye_pipeline/issues/1)。找到下载或者clone的文件夹，修改这两个文件
  - `__init__.py`：添加`from .bullseye_pipeline import create_bullseye_pipeline`
  - `bullseye_pipeline.py`：将`from utils import *`改为`from .utils import *`（将注释的行换一下就行）
- 运行时报错：因为这个包没有写对环境的要求，但我的nibabel和numpy的版本高一些，他们分别的`nib.get_data()`和`np.bool`已经被弃用。
  - `nibabel.deprecator.ExpiredDeprecationError: get_data() is deprecated in favor of get_fdata(), which has a more predictable return type. To obtain get_data() behavior going forward, use numpy.asanyarray(img.dataobj).`：找到安装位置（比如anaconda的site-packages中）下的`utils.py`文件，将其中的`nib.get_data()`都改为`nib.get_fdata()`，一共10处，注意不要改成了`nib.header.get_data_shape()`。
  - ``` `np.bool` was a deprecated alias for the builtin `bool`. To avoid this error in existing code, use `bool` by itself. Doing this will not modify any behavior and is safe. If you specifically wanted the numpy scalar type, use `np.bool_` here. ```：类似前者，将`norm_dist_map`函数中`np.bool`替换为`np.bool_`，一共2处。
  - `ValueError: invalid literal for int() with base 10: '4.01.0'`：完成上面两处修改后会出现这个问题，或许和`nib.get_fdata()`有关？类似前者，在`merge_labels`函数中`out[mask3] = int(str(u1) + str(u2))`一行上面增添：
  ```u1 = int(u1)```
  ```u2 = int(u2)```
  以去除小数。

## 运行

时隔一段时间，发现重新安装实在是困难（python2.7早已经被弃用，相应的依赖版本也已经太老），所以可以按照上面的修改修改文件夹内的源文件（但不用修改`from utils import *`），然后直接调用run_bullseye_pipeline.py这个脚本文件，而不按照官方文档安装。

经过测试可以适用于Freesurfer 8.0.0-beta的输出（官方文档中测试于Freesurfer 6，但依赖的那些文件应该是各个版本都有的）