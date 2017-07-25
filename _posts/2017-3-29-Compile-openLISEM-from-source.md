
+ 1.[下载MinGW-w64](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/)，我下载的是[x86_64-4.9.3-release-posix-sjlj-rt_v4-rev1.7z](https://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win64/Personal%20Builds/mingw-builds/4.9.3/threads-posix/sjlj/x86_64-4.9.3-release-posix-sjlj-rt_v4-rev1.7z/download)，并解压至C盘根目录，如`C:/mingw64`，将`C:/mingw64/bin`添加至环境变量`PATH`中。
	> If your programe include STL <thread> on windows and error occurred that "'thread' is not a member of 'std'", you will need Mingw with **posix-threads**. You can choose between sjlj and seh. Seh is only x64 and sjlj is both x32 and x64.
+ 2.[下载msys](https://sourceforge.net/projects/mingwbuilds/files/external-binary-packages/msys%2B7za%2Bwget%2Bsvn%2Bgit%2Bmercurial%2Bcvs-rev13.7z/download)，解压至步骤1的路径中，如`C:/mingw64/msys`。
+ 3.电脑中应该装有Visual Studio 2010或以上版本，并将编译器路径添加至环境变量，如我安装了VS2013：
	```
	C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin
	C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin\amd64
	```
+ 4. 以管理员身份打开`C:/mingw64/msys/bin/msys.bat`
+ 5. 编译GDAL

	```
	cmake -G "Unix Makefiles" -DCMAKE_MAKE_PROGRAM=c:/mingw64/bin/mingw32-make -DCMAKE_CONFIGURATION_TYPES=Release -DCMAKE_BUILD_TYPE=Release -Dpeacock_download_dir=C:/Downloads -Dpeacock_prefix="C:/peacock" -Dbuild_gdal=true -Dgdal_build_ogr=true -Dgdal_build_python_package=true -Dgdal_version=1.11.5 C:/peacock
	
	make
	```
    + 5.1. 这样是仅编译Release版本，如果想编译多个版本，则去掉`DCMAKE_CONFIGURATION_TYPES=Release -DCMAKE_BUILD_TYPE=Release`。
    
	+ 5.2. 不出意外地，开始自动编译，直至遇到第一个错误，错误提示为：
		```
		/bin/sh: /c/mingw64/bin/ar: Bad file number
		GNUmakefile:33: recipe for target `D:/compile/peacock/gdal-1.11.5-Debug-prefix/src/gdal-1.11.5-Debug/libgdald.a' failed
		make[4]: *** [D:/compile/peacock/gdal-1.11.5-Debug-prefix/src/gdal-1.11.5-Debug/libgdald.a] Error 126
		make[3]: *** [check-lib] Error 2
		CMakeFiles/gdal-1.11.5-Debug.dir/build.make:117: recipe for target `gdal-1.11.5-Debug-prefix/src/gdal-1.11.5-Debug-stamp/gdal-1.11.5-Debug-build' failed
		make[2]: *** [gdal-1.11.5-Debug-prefix/src/gdal-1.11.5-Debug-stamp/gdal-1.11.5-Debug-build] Error 2
		CMakeFiles/Makefile2:137: recipe for target `CMakeFiles/gdal-1.11.5-Debug.dir/all' failed
		make[1]: *** [CMakeFiles/gdal-1.11.5-Debug.dir/all] Error 2
		Makefile:83: recipe for target `all' failed
		make: *** [all] Error 2   
		```
	解决方法参考： https://trac.osgeo.org/gdal/ticket/3465
	解决思路为：手动编译`libgdald.a`和`libgdald.dll` (Debug版本，Release版本则去掉`d`)

		```
		cd gdal-1.11.5-Debug-prefix\src\gdal-1.11.5-Debug
		
		ar r ./libgdal.a ./frmts/o/*.o ./gcore/*.o ./port/*.o ./alg/*.o ./apps/commonutils.o ./apps/gdalinfo.o ./apps/gdal_translate.o ./apps/gdalwarp.o ./apps/ogr2ogr.o ./apps/gdaldem.o ./apps/nearblack.o ./apps/gdal_grid.o ./apps/gdal_rasterize.o ./apps/gdalbuildvrt.o ./ogr/ogrsf_frmts/o/*.o ./ogr/ogrgeometryfactory.o ./ogr/ogrpoint.o ./ogr/ogrcurve.o ./ogr/ogrlinestring.o ./ogr/ogrlinearring.o ./ogr/ogrpolygon.o ./ogr/ogrutils.o ./ogr/ogrgeometry.o ./ogr/ogrgeometrycollection.o ./ogr/ogrmultipolygon.o ./ogr/ogrsurface.o ./ogr/ogrmultipoint.o ./ogr/ogrmultilinestring.o ./ogr/ogr_api.o ./ogr/ogrfeature.o ./ogr/ogrfeaturedefn.o ./ogr/ogrfeaturequery.o ./ogr/ogrfeaturestyle.o ./ogr/ogrfielddefn.o ./ogr/ogrspatialreference.o ./ogr/ogr_srsnode.o ./ogr/ogr_srs_proj4.o ./ogr/ogr_fromepsg.o ./ogr/ogrct.o ./ogr/ogr_opt.o ./ogr/ogr_srs_esri.o ./ogr/ogr_srs_pci.o ./ogr/ogr_srs_usgs.o ./ogr/ogr_srs_dict.o ./ogr/ogr_srs_panorama.o ./ogr/ogr_srs_ozi.o ./ogr/ogr_srs_erm.o ./ogr/swq.o ./ogr/swq_expr_node.o ./ogr/swq_parser.o ./ogr/swq_select.o ./ogr/swq_op_registrar.o ./ogr/swq_op_general.o ./ogr/ogr_srs_validate.o ./ogr/ogr_srs_xml.o ./ogr/ograssemblepolygon.o ./ogr/ogr2gmlgeometry.o ./ogr/gml2ogrgeometry.o ./ogr/ogr_expat.o ./ogr/ogrpgeogeometry.o ./ogr/ogrgeomediageometry.o ./ogr/ogr_geocoding.o ./ogr/osr_cs_wkt.o ./ogr/osr_cs_wkt_parser.o ./ogr/ogrgeomfielddefn.o
		
		x86_64-w64-mingw32-g++ -std=c++11 -Wzero-as-null-pointer-constant -DNULL_AS_NULLPTR -shared ./frmts/o/*.o ./gcore/*.o ./port/*.o ./alg/*.o ./ogr/ogrsf_frmts/o/*.o ./ogr/ogrgeometryfactory.o ./ogr/ogrpoint.o ./ogr/ogrcurve.o ./ogr/ogrlinestring.o ./ogr/ogrlinearring.o ./ogr/ogrpolygon.o ./ogr/ogrutils.o ./ogr/ogrgeometry.o ./ogr/ogrgeometrycollection.o ./ogr/ogrmultipolygon.o ./ogr/ogrsurface.o ./ogr/ogrmultipoint.o ./ogr/ogrmultilinestring.o ./ogr/ogr_api.o ./ogr/ogrfeature.o ./ogr/ogrfeaturedefn.o ./ogr/ogrfeaturequery.o ./ogr/ogrfeaturestyle.o ./ogr/ogrfielddefn.o ./ogr/ogrspatialreference.o ./ogr/ogr_srsnode.o ./ogr/ogr_srs_proj4.o ./ogr/ogr_fromepsg.o ./ogr/ogrct.o ./ogr/ogr_opt.o ./ogr/ogr_srs_esri.o ./ogr/ogr_srs_pci.o ./ogr/ogr_srs_usgs.o ./ogr/ogr_srs_dict.o ./ogr/ogr_srs_panorama.o ./ogr/ogr_srs_ozi.o ./ogr/ogr_srs_erm.o ./ogr/swq.o ./ogr/swq_expr_node.o ./ogr/swq_parser.o ./ogr/swq_select.o ./ogr/swq_op_registrar.o ./ogr/swq_op_general.o ./ogr/ogr_srs_validate.o ./ogr/ogr_srs_xml.o ./ogr/ograssemblepolygon.o ./ogr/ogr2gmlgeometry.o ./ogr/gml2ogrgeometry.o ./ogr/ogr_expat.o ./ogr/ogrpgeogeometry.o ./ogr/ogrgeomediageometry.o ./ogr/ogr_geocoding.o ./ogr/osr_cs_wkt.o ./ogr/osr_cs_wkt_parser.o ./ogr/ogrgeomfielddefn.o ./libgdal.a  -lz -lws2_32 -liconv -o ./libgdal.dll
		```
	+ 5.3. 编译成功后继续：
		```
		mingw32-make
		```
    + 5.4. GDAL库可以正常编译完成，但是GDAL for python会遇到错误。
    ```
	C:\Users\ZhuLJ\AppData\Local\Programs\Common\Microsoft\Visual C++ for Python\9.0\VC\Bin\amd64\link.exe /DLL /nologo /INCREMENTAL:NO /LIBPATH:../../.libs /LIBPATH:../../ /LIBPATH:C:\Python27x64\libs /LIBPATH:C:\Python27x64\PCbuild\amd64 gdal_i.lib /EXPORT:init_gdal build\temp.win-amd64-2.7\Release\extensions/gdal_wrap.obj /OUT:build\lib.win-amd64-2.7\osgeo\_gdal.pyd /IMPLIB:build\temp.win-amd64-2.7\Release\extensions\_gdal.lib /MANIFESTFILE:build\temp.win-amd64-2.7\Release\extensions\_gdal.pyd.manifest
	LINK : fatal error LNK1181: cannot open input file 'gdal_i.lib'
	error: command '"C:\Users\ZhuLJ\AppData\Local\Programs\Common\Microsoft\Visual C++ for Python\9.0\VC\Bin\amd64\link.exe"' failed with exit status 1181
	GNUmakefile:63: recipe for target 'build' failed
	mingw32-make[2]: *** [build] Error 1
	mingw32-make[2]: Leaving directory 'd:/compile/peacock/gdal-1.11.5-Release-prefix/src/gdal-1.11.5-Release/swig/python'
	GNUmakefile:29: recipe for target 'build' failed
	mingw32-make[1]: *** [build] Error 2
	mingw32-make[1]: Leaving directory 'd:/compile/peacock/gdal-1.11.5-Release-prefix/src/gdal-1.11.5-Release/swig'
	GNUmakefile:80: recipe for target 'swig-modules' failed
	mingw32-make: *** [swig-modules] Error 2
	```
	通过错误提示可以看到，Python默认的编译器为`Visual C++ for Python`，而GDAL的Python是用SWIG封装，我们期望用mingw32编译。
    因此我们首先要下载编译好的swigwin，并将其路径添加至环境变量PATH中。
	`cd D:\compile\peacock\gdal-1.11.5-Release-prefix\src\gdal-1.11.5-Release\swig\python`
	然后打开setup.cfg文件，临时指定编译器为mingw32

    ```
	[build]
	compiler=mingw32
    ```
	
    如何在Windows下利用mingw32编译Python库，我们安装的python多数是通过MSVC编译的，因此需要用一个工具`pexports`根据`python.lib`和`python27.dll`生成`libpython27.a`，放在Python目录的libs下，参考[Building Python modules on MS Windows platform with mingw32](http://old.zope.org/Members/als/tips/win32_mingw_modules)和[MinGW: pexports for Windows DLLs](https://m8051.blogspot.com/2010/12/mingw-pexports-for-windows-dlls.html)。
    如果出现无法识别的命令`-mno-cygwin`，参考[这个解决方案](http://stackoverflow.com/questions/13592192/compiling-pygraphviz-unrecognized-command-line-option-mno-cygwin)。
    `setup.py`中读取`gdal-config`中设置的函数在windows的cmd下无法运行，利用mingw\bin下的bash.exe就可以，参考[这个解决方案](http://stackoverflow.com/questions/41645415/execute-bash-script-from-python-on-windows)。

	准备工作做完之后，可以开始编译Python bindings啦：
	```
	make generate
	make build
	```
	
	如果出现`undefined reference to inflateInit2_`之类的链接错误，说明没有正确配置zlib库，可利用mingw32源码编译一次后，重新编译GDAL

	此外，还需手动修改一下`swig/python/setup.py`文件。

	

 


## boost

cmake -G "Unix Makefiles" -DCMAKE_MAKE_PROGRAM=c:/mingw64/bin/mingw32-make -Dpeacock_download_dir=C:/Downloads -Dpeacock_prefix="C:/peacock" -Dbuild_boost=true -Dboost_version=1.57.0 C:/peacock

直接mingw32-make 会出现错误
D:\compile\peacock2>mingw32-make
[ 12%] Performing configure step for 'boost-1.57.0'
./bootstrap.bat: line 1: @ECHO: command not found
./bootstrap.bat: line 3: syntax error near unexpected token `('
./bootstrap.bat: line 3: `REM Copyright (C) 2009 Vladimir Prus'
CMakeFiles/boost-1.57.0.dir/build.make:108: recipe for target 'boost-1.57.0-prefix/src/boost-1.57.0-stamp/boost-1.57.0-configure' failed
mingw32-make[2]: *** [boost-1.57.0-prefix/src/boost-1.57.0-stamp/boost-1.57.0-configure] Error 2
CMakeFiles/Makefile2:67: recipe for target 'CMakeFiles/boost-1.57.0.dir/all' failed
mingw32-make[1]: *** [CMakeFiles/boost-1.57.0.dir/all] Error 2
makefile:83: recipe for target 'all' failed
mingw32-make: *** [all] Error 2


cd boost-1.57.0-prefix\src\boost-1.57.0

.\bootstrap.bat mingw
.\b2 toolset=gcc install --prefix=C:\peacock\windows\windows\mingw-4\x86_64


```cmake
cmake -G "Unix Makefiles" -DCMAKE_MAKE_PROGRAM=c:/mingw64/bin/mingw32-make -Dpeacock_download_dir=C:/Downloads -Dpeacock_prefix="C:/peacock" -Dbuild_boost=true -Dboost_version=1.59.0 -Dbuild_qt=true -Dqt_version=4.8.6 -Dbuild_qwt=true -Dqwt_version=6.1.2 -Dbuild_pcraster_raster_format=true -Dpcraster_raster_format_version=1.3.1 C:/peacock
```

## Qt4
https://wiki.qt.io/Building_Qt_Desktop_for_Windows_with_MinGW#Build_Qt_with_MinGW_for_a_x64_.28x86_64.29_target

set PATH=C:\mingw64\bin;C:\mingw64\msys\bin;

cmake -G "Unix Makefiles" -DCMAKE_MAKE_PROGRAM=c:/mingw64/bin/mingw32-make -Dpeacock_download_dir=C:/Downloads -Dpeacock_prefix="C:/peacock" -Dbuild_qt=true -Dqt_version=4.8.6 C:/peacock

mingw32-make CC=x86_64-w64-mingw32-gcc CXX=x86_64-w64-mingw32-g++ LINK=x86_64-w64-mingw32-g++

出现错误，/mingw/lib/libmingw32.a(main.o):main.c:(.text+0x106): undefined reference to `WinMain@16
collect2.exe: error: ld returned 1 exit status

Google了一下发现在mingw32-make命令后加上-mwindows可以解决。

mingw32-make install 发现并没有install的配置，因此
cd D:\compile\qt\qt-4.8.6-prefix\src\qt-4.8.6
再次运行mingw32-make install 即可顺利安装至C:\peacock\windows\windows\mingw-4\x86_64

## qwt
同样为了避免其他路径干扰，临时设置几个用得到的：
set PATH=C:\Program Files\CMake\bin;C:\mingw64\bin;C:\mingw64\msys\bin;C:\peacock\windows\windows\mingw-4\x86_64\bin

cmake -G "Unix Makefiles" -DCMAKE_MAKE_PROGRAM=c:/mingw64/bin/mingw32-make -Dpeacock_download_dir=C:/Downloads -Dpeacock_prefix="C:/peacock" -Dbuild_qwt=true -Dqwt_version=6.1.2 C:/peacock

mingw32-make
不用install，C:\peacock\windows\windows\mingw-4\x86_64路径下已经有了qwt

## pcraster_raster_format
cmake -G "Unix Makefiles" -DCMAKE_MAKE_PROGRAM=c:/mingw64/bin/mingw32-make -Dpeacock_download_dir=C:/Downloads -Dpeacock_prefix="C:/peacock" -Dbuild_pcraster_raster_format=true -Dpcraster_raster_format_version=1.3.1 C:/peacock

mingw32-make

## fern

peacock中没有设置Fern的库地址，所以不可用peacock安装Fern。
需要我们从Github下载并手动编译，地址为https://github.com/geoneric/fern。

set PATH=C:\Program Files\CMake\bin;C:\mingw64\bin;C:\mingw64\msys\bin;C:\peacock\windows\windows\mingw-4\x86_64\bin;C:\peacock\windows\windows\mingw-4\x86_64\include;C:\Python27x64\Scripts

cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug -DFERN_ALGORITHM:BOOL=TRUE ..
the latest version: cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug -DFERN_BUILD_DOCUMENTATION:BOOL=FALSE -DFERN_BUILD_ALGORITHM:BOOL=TRUE ..

cmake --build . --config Debug --target all


 -DCMAKE_MAKE_PROGRAM=c:/mingw64/bin/mingw32-make -Dpeacock_download_dir=C:/Downloads -Dpeacock_prefix="C:/peacock" -Dbuild_fern=true C:/peacock

mingw32-make