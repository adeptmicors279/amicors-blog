---
title: 记一次paddleocr打包问题：FileNotFoundError以及ModuleNotFoundError
comments: true
tags:
  - Python
category: Python
image: 'https://bucket.iczrx.cn/2024/05/03/6633bdefd8268.png'
top_img: 'https://t.mwm.moe/pc'
abbrlink: 6793f133
published: 2024-05-03 00:17:00
---

# 为什么打包paddleocr时会报错

在处理报错之前，我们先看看报错内容

`FileNotFoundError: [Errno 2] No such file or directory: 'E:\\python\\python3.11(64bit)\\dist\\demo1\\_internal\\paddleocr\\tools/__init__.py'`

![报错内容如下](https://bucket.iczrx.cn/2024/05/03/6633bdefd8268.png)

报错filenofound

未找到文件，看起来貌似是打包时没有把文件打包全

## 处理问题

进入你创建的虚拟环境（当然你不创建也没关系）

输入`pyinstall -w -D 你的路径`进行不完满打包

然后打开`dist`里的`_internal`目录

把你的Python解释器里安装的paddleocr模块全部复制过来



这时候，你就会发现，欸，还是会报错，但是内容变了

变成了：`ModuleNotFoundError: No module named 'shapely'`

别慌，按照上述方法，继续把python解释器里site-packages里的模块给复制过来

```
ModuleNotFoundError: No module named 'pyclipper'
	ModuleNotFoundError: No module named 'skimage'
	ModuleNotFoundError: No module named 'pywt'
	ModuleNotFoundError: No module named 'imgaug'
	ModuleNotFoundError: No module named 'imageio'
	ModuleNotFoundError: No module named 'fasttext'
	ModuleNotFoundError: No module named 'lmdb'

	ModuleNotFoundError: No module named 'imghdr'  
	ModuleNotFoundError: No module named 'fasttext_pybind'

	ImportError: cannot import name 'signal' from 'scipy' (你的路径\scipy\__init__.pyc)
```

发生以下报错解决方法同上

然后你就会发现

打包完的程序可以运行了

当然，请善用Windows的搜索功能

如果懒得用，直接在IDE开发环境里敲

`import 模块名`

然后再按住CTRL鼠标左键即可进入模块的py文件

顶部查看一下路径即可

## 那么，为什么会有这种问题呢

那么，你得问pyinstaller为什么打包不全





# OCR下载地址

<https://github.com/txptxp1/OCR-to-translate>

这是我写的一个OCR翻译器

由于paddleocr体积大

我接入的本地ocr就是paddleocr

所以导致我打包之后的软件体积也巨大

解决方案要么去掉，要么给个选项运行的时候直接从服务器下载paddleocr文件然后本地部署



# 最后

参考文章[paddleocr 打包问题_paddleocr打包太难了-CSDN博客](https://blog.csdn.net/m0_46875088/article/details/122096159)
