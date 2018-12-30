#ubuntu14.04安装完必要package如###Ｌinux中所示，之后，按如下步骤

find . -iname platform.arm

gedit ./ThirdParty/PSCommon/BuildSystem/Platform.Arm

找到：
CFLAGS += -march=armv7-a -mtune=cortex-a9 -mfpu=neon -mfloat-abi=softfp #-mcpu=cortex-a8

修改为：
CFLAGS += -march=armv7-a -mtune=cortex-a15 -mfpu=neon-vfpv4 -mfloat-abi=hard 	

打开gedit，修改CommonCppMakefile文件	

gedit ThirdParty/PSCommon/BuildSystem/CommonCppMakefile

找到这两行：

LDFLAGS += -Wl,-rpath ./

OUTPUT_COMMAND = (CXX)−o(OUTPUT_FILE) (OBJFILES)(LDFLAGS)

在这两行代码中间加入以下代码：

ifneq (“$(OSTYPE)”,”Darwin”)

    LDFLAGS += -lpthread
    
endif

保存退出。

添加例程以检查安装是否正确

gedit Makefile

在文件末尾添加：

core_samples: $(CORE_SAMPLES)

保存退出。

开始编译	

make

make core_samples

GLUT_SUPPORTED=1 make tools(这一步可能会有问题，不过不影响)

OpenNI2测试 

cd ~/openni2/OpenNI2/Bin/x64-Release

连接Xtion Pro Live到你的电脑 

./SimpleRead

终端有数字输出

./NiViewer

有图像输出1,2,3,...，+分别对应不同数据流 

有时需要sudo

正式安装OpenNI2 

进入Packaging/Linux文件夹

sudo sh install.sh

将“lib”文件（动态链接库）和“include”文件（头文件）复制到系统路径下

进入OpenNI2目录

sudo cp -r Include /usr/include/openni2

sudo cp -r Bin/x64-Release/OpenNI2 /usr/lib/

sudo cp Bin/x64-Release/libOpenNI2.* /usr/lib/

更新 library cache:

sudo ldconfig

设置传感器权限

sudo usermod -a -G video Ubuntu （Ubuntu为你的用户名）

创建一个包配置文件

sudo gedit /usr/lib/pkgconfig/libopenni2.pc

用一下代码覆盖

prefix=/usr

exec_prefix=${prefix}

libdir=${exec_prefix}/lib

includedir=${prefix}/include/openni2


Name: OpenNI2

Description: A general purpose driver for all OpenNI cameras.

Version: 2.2.0.0

Cflags: -I${includedir}

Libs: -L${libdir} -lOpenNI2 -L${libdir}/OpenNI2/Drivers -lDummyDevice -lOniFile -lPS1080.so

确保其能被正确找到，运行：

pkg-config --modversion libopenni2

此时，终端会有2.2.0.0回复 

至此，OpenNI2已经安装好了，并可以驱动Xtion了

# OpenNI

http://structure.io/openni

## Develop branch ##

The latest ongoing development is currently being done in the develop branch.  Refer to README and ReleasesNotes in the develop branch for up to date build instructions.

## Contributing ##

Pull requests that do not apply cleanly on top of the [`develop` branch head](http://github.com/occipital/OpenNI2/tree/develop) will be rejected.

Other than that, sensible and meaningful contributions are very welcome!

## Building Prerequisites

### Windows

- Microsoft Visual Studio 2010

    - Download and install from: http://msdn.microsoft.com/en-us/vstudio/bb984878.aspx

- Microsoft Kinect SDK v1.6

    - Download and install from: http://go.microsoft.com/fwlink/?LinkID=262831

- Python 2.6+/3.x

    - Download and install from: http://www.python.org/download/

- PyWin32

    - Download and install from: http://sourceforge.net/projects/pywin32/files/pywin32/
    
    Please make sure you download the version that matches your exact python version.

- JDK 6.0

    - Download and install from: http://www.oracle.com/technetwork/java/javase/downloads/jdk-6u32-downloads-1594644.html
    
    You must also define an environment variable called `JAVA_HOME` that points to the JDK installation directory. For example:

    	set JAVA_HOME=c:\Program Files (x86)\Java\jdk1.6.0_32

- WIX 3.5

    - Download and install from: http://wix.codeplex.com/releases/view/60102

- Doxygen

    - Download and install from: http://www.stack.nl/~dimitri/doxygen/download.html#latestsrc

- GraphViz

    - Download and install from: http://www.graphviz.org/Download_windows.php

### Linux

- GCC 4.x

	- Download and install from: http://gcc.gnu.org/releases.html

    - Or use `apt`:
    	
	    	sudo apt-get install g++

- Python 2.6+/3.x

    - Download and install from: http://www.python.org/download/

    - Or use `apt`:

	    	sudo apt-get install python

- LibUSB 1.0.x

    - Download and install from: http://sourceforge.net/projects/libusb/files/libusb-1.0/

    - Or use `apt`:

	    	sudo apt-get install libusb-1.0-0-dev

- LibUDEV

		sudo apt-get install libudev-dev

- JDK 6.0

    - Download and install from: http://www.oracle.com/technetwork/java/javase/downloads/jdk-6u32-downloads-1594644.html

    - Or use `apt`:
    
    	- On Ubuntu 10.x:

				sudo add-apt-repository "deb http://archive.canonical.com/ lucid partner"
				sudo apt-get update
				sudo apt-get install sun-java6-jdk

    	- On Ubuntu 12.x（及以上）:

				sudo apt-get install openjdk-6-jdk

- FreeGLUT3

    - Download and install from: http://freeglut.sourceforge.net/index.php#download

    - Or use `apt`:

	    	sudo apt-get install freeglut3-dev

- Doxygen

    - Download and install from: http://www.stack.nl/~dimitri/doxygen/download.html#latestsrc

    - Or use `apt`:
    
    		sudo apt-get install doxygen

- GraphViz

    - Download and install from: http://www.graphviz.org/Download_linux_ubuntu.php

    - Or use `apt`:
    
    		sudo apt-get install graphviz

### Android

- Download and install the Android NDK version **r8d**. Newer versions will **NOT** work.

- For Mac OS X: http://dl.google.com/android/ndk/android-ndk-r8d-darwin-x86.tar.bz2
- For Windows:  http://dl.google.com/android/ndk/android-ndk-r8d-windows.zip
- For Linux:    http://dl.google.com/android/ndk/android-ndk-r8d-linux-x86.tar.bz2

    Building Android packages requires the NDK_ROOT environment variable to be defined, and its value must be pointing to the NDK installation dir: `NDK_ROOT=/path/to/android-ndk-r8d`

## Building

### Building on Windows:

  Open the solution `OpenNI.sln`

### Building on Linux:

  Run:

	make

### Cross-Compiling for ARM on Linux

  The following environment variables should be defined:

- `ARM_CXX=path-to-cross-compilation-g++`
- `ARM_STAGING=path-to-cross-compilation-staging-dir`

Then, run:

	PLATFORM=Arm make

### Creating OpenNI2 packages

  - Go into the directory `Packaging`
  - Run:

		ReleaseVersion.py [x86|x64|arm|android]

  - The installer will be placed in the `Final` directory
