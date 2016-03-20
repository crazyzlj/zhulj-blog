---
layout: post
title: "Python Environment Configuration for GISer"
category: [Python]
tag: [Python, Arcpy, GIS]
date: 2016-03-17 17:00:00
comments: true
---

**1. ArcGIS 10.3, Arcpy, GDAL etc.**
======

ArcGIS Desktop 10.3开始安装时必须选择安装Python，好在可通过ArcGIS_BackgroundGP_for_Desktop_103_141996.exe安装支持64位python的arcpy。
这样，装完ArcGIS10.3之后我们就有了32位和64位的Python 2.7.8，分别位于`C:\Python27\ArcGIS10.3`和`C:\Python27\ArcGISx6410.3`。
我们以使用64位Python为例进行配置，将`C:\Python27\ArcGISx6410.3;C:\Python27\ArcGISx6410.3\Scripts`添加至环境变量Path中;
但是此时如果右键以IDLE打开某py脚本，默认的python是32位版本的，需要做如下设置：

	注册表中找到
	HKEY_CLASSES_ROOT\\Python.CompiledFile\\shell\\open\\command\\(default)
	将其value修改为"C:\Python27\ArcGISx6410.3\python.exe" "%1" %*

如果是自己另外安装一个版本的Python，如我在`C:/Python27x64`下安装了64位的2.7.8，如果想在这个版本里调用arcpy，需要在`C:\Python27x64\Lib\site-packages`下新建一个文件`Desktop10.3.pth`，文件内容如下：

	C:\Program Files (x86)\ArcGIS\Desktop10.3\bin64
	C:\Program Files (x86)\ArcGIS\Desktop10.3\ArcPy
	C:\Program Files (x86)\ArcGIS\Desktop10.3\ArcToolBox\Scripts

**2. easy_install and pip**
======

管理员方式运行cmd，cd到setuptools所在目录，输入`python setup.py install`；
同样，cd到pip所在目录，输入`python setup.py install`
这样，我们就可以离线安装whl格式的python第三方库啦，下载地址为http://www.lfd.uci.edu/~gohlke/pythonlibs/。

**3. GDAL and other packages**
======

通过上述链接，可以下载完整的GDAL打包文件GDAL-1.11.3-cp27-none-win_amd64.whl，管理员方式运行cmd，cd到该文件目录，输入pip install GDAL-1.11.3-cp27-none-win_amd64.whl，提示安装成功即可，输入以下命令验证是否安装成功：
	
```python
>>> python
>>> from osgeo import gdal
>>> from osgeo import ogr
>>> from osgeo import osr
>>> from osgeo import gdal_array
>>> from osgeo import gdalconst
```

其他python库安装，步骤类似：scipy，numexpr，PyQt4，wxPython（需要wxPython_common），PyGTK，python_dateutil，pytz，matplotlib，pywin32...
	
Put setuptools, pip, and other *.whl under the same folder, the batch process code could help you to install all of them automatically in PC. [Download batch file](http://zhulj-blog.oss-cn-beijing.aliyuncs.com/install.bat "install_python_packages_whl")

**4. PyCharm**
=====
	
非常好用的Python IDE


**5. Common errors**
====
- Error of **pywin32**
		
Description:

```python
>>> import win32com.client
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "C:\Program Files (x86)\JetBrains\PyCharm Community Edition 5.0.3\helpers\pydev\pydev_import_hook.py", line 21, in do_import
    module = self._system_import(name, *args, **kwargs)
  File "C:\Python27x64\lib\site-packages\win32com\__init__.py", line 5, in <module>
    import win32api, sys, os
  File "C:\Program Files (x86)\JetBrains\PyCharm Community Edition 5.0.3\helpers\pydev\pydev_import_hook.py", line 21, in do_import
    module = self._system_import(name, *args, **kwargs)
ImportError: DLL load failed: The specified module could not be found.
>>> import win32api
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "C:\Program Files (x86)\JetBrains\PyCharm Community Edition 5.0.3\helpers\pydev\pydev_import_hook.py", line 21, in do_import
    module = self._system_import(name, *args, **kwargs)
ImportError: DLL load failed: The specified module could not be found.
```

Solution:

```python

Copy
C:\Python26\Lib\site-packages\pywin32_system32\*
to
C:\Windows\System32
Then, try this again in Python
>>> import win32api
>>> import win32com.client
```
