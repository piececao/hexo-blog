---
title: 树莓派pico开发环境搭建(Pico SDK)
date: 2025-02-11 22:16:21
tags: ['嵌入式', 'rp2040', '环境搭建', 'Pico SDK']
---
# PICO上手

树莓派pico的板子很小一片，和STM32 Blue Pill差不多的大小。上面有USB接口，可以通过USB烧录uf2格式的程序文件。

PICO的主控室RP2040，具有：

- 双核 Arm Cortex-M0 + @ 133MHz

- 内置 264KB SRAM 和 2MB 的板载闪存

- 专用 QSPI 总线

- DMA 控制器

- 30 个 GPIO 引脚，其中 4 个可用作模拟输入

- 2 个 UART、2 个 SPI 控制器和 2 个 I2C 控制器

- 16 个 PWM 通道

- USB 1.1 主机和设备支持

- 8 个树莓派可编程 I/O（PIO）状态机，用于自定义外围设备支持

- 支持 UF2 的 USB 大容量存储启动模式，用于拖放式编程

![树莓派Pico引脚图](assets/2025-02-19-16-13-37-image.png)

# 烧录

下载程序文件[blink.uf2](https://datasheets.raspberrypi.com/soft/blink.uf2)，按住pico板子上的“BOOTSEL”按键，然后用USB数据线把pico连接到电脑上，在电脑上就可以看到连接了一个USB设备“RPI-RP2”

![](assets/2025-02-19-16-41-46-image.png)

把blink.uf2拖进去就可以烧录了。烧录完成USB会自动断开连接，板子开始执行程序文件。

![](assets/2025-02-19-16-53-34-image.png)

# 搭建开发环境（简易版）

首先你需要一个[VSCode](https://code.visualstudio.com/)

在vscode的扩展商店里面搜索安装Raspberry Pi Pico扩展即可

**缺点：这种方式特别依赖网络，由于众所周知的原因，不建议使用这种方法** 

![](assets/2025-02-19-18-12-08-image.png)

# 手动配置Pico SDK

## 安装必要工具

除了上面提到的[VSCode](https://code.visualstudio.com/)，还需要安装gcc交叉编译器、cmake。

### Windows

Windows下可以在[arm网站](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)上下载交叉编译工具，安装或者解压后把bin目录[添加到PATH中](https://cn.bing.com/search?pc=MOZI&form=MOZLBR&q=%E6%B7%BB%E5%8A%A0path)。[下载](https://github.com/Kitware/CMake/releases/download/v4.0.0-rc2/cmake-4.0.0-rc2-windows-x86_64.msi)安装[cmake](https://cmake.org/download/)，安装的时候勾选“添加 
CMake 到环境变量”(Add CMake to the PATH environment variable)。

下载Visual Studio（用于nmake）

![](assets/2025-03-03-13-49-16-f33299f0-28a4-4a5e-af45-bee6ccc6ae0f.png)

勾选“使用C++的桌面开发”

![](assets/2025-03-02-22-36-57-image.png)

打开cmd(win+x -> 运行 -> 输入cmd -> 确定)，测试工具是否安装成功。

输入`cmake`，回车

![](assets/2025-03-02-22-38-04-image.png)

> 失败

![](assets/2025-03-02-22-38-55-image.png)

> 成功

输入`arm-none-eabi-gcc`，回车

![](assets/2025-03-02-22-39-29-image.png)

> 成功

安装[Python3](https://www.python.org/ftp/python/3.13.2/python-3.13.2-amd64.exe)

![](assets/2025-03-02-22-41-47-image.png)

同样勾选添加到path

[安装Git](https://zhuanlan.zhihu.com/p/443527549)

### Linux

以Debian系为例：

```shell-session
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi libstdc++-arm-none-eabi-newlib
```

结束！

## 下载Pi Pico SDK

在pico-sdk的[github release](https://github.com/raspberrypi/pico-sdk/releases)页面下载pico-sdk-x.x.x.tar.gz，解压

![](assets/2025-03-02-22-36-32-image.png)

## 编译Picotool（Windows）

在Github上下载[libusb](https://github.com/libusb/libusb/releases)并解压

![](assets/2025-03-03-14-07-49-image.png)

打开“Developer Command Prompt for VS 2022”然后git clone一下picotool的源码：

```batch
REM 克隆picotool的源代码
git clone https://github.com/raspberrypi/picotool.git
mkdir build
cd build
REM PICO_SDK_PATH 是pico-sdk的路径，LIBUSB是前面的解压的libusb的路径
REM CMAKE_INSTALL_PREFIX是指定的picotool的安装路径
cmake -G "NMake Makefiles" .. -DPICO_SDK_PATH=D:\Programs\pico-sdk -DLIBUSB_ROOT=D:\Programs\libusb -DCMAKE_INSTALL_PREFIX=D:\Programs\picotool_bin -DPICOTOOL_FLAT_INSTALL=1
REM 看到提示“-- Build files have been written to: xxxxxx”说明cmake配置成功，可以用nmake开始编译了
nmake
REM 把picotool安装到CMAKE_INSTALL_PREFIX
nmake install
```

nmake结束后，在picotool安装目录下可以看到一个picotool文件夹

![](assets/2025-03-03-15-03-15-image.png)

# 创建新工程

把pico-sdk目录下的`external/pico_sdk_import.cmake`复制到你的工程目录下

![](assets/2025-03-02-22-36-08-image.png)就像这样

在工程目录下新建一个`CMakeLists.txt`，输入如下内容：

```cmake
cmake_minimum_required(VERSION 3.13)

set(PICO_SDK_PATH "D:\\Programs\\pico-sdk")
set(PICO_BOARD "pico")
set(picotool_DIR "D:\\Programs\\picotool_bin\picotool")

# initialize the SDK based on PICO_SDK_PATH
# note: this must happen before project()
include(pico_sdk_import.cmake)

project(my_project)

# initialize the Raspberry Pi Pico SDK
pico_sdk_init()

# rest of your project
```

`my_project是工程名字`；`PICO_SDK_PATH`是pico-sdk的目录；`picotool_DIR`是picotool的所在目录；`PICO_BOARD`是pico板子型号，在pico-sdk目录的`srd/boards/include/boards`目录下可以看到完整开发板支持列表。

新建一个hello.c：

```c
#include <stdio.h>
#include "pico/stdlib.h"

int main() {
    setup_default_uart();
    while(1){
        printf("hello, world\r\n");
        sleep_ms(1000);
    }
    return 0;
}
```

然后在`CMakeLists.txt`的末尾添加如下内容：

```cmake
# 添加hello.c源文件
add_executable(hello
    hello.c
)

# 链接pico_stdlib库
# Add pico_stdlib library which aggregates commonly used features
target_link_libraries(hello pico_stdlib)

# 转换为uf2文件以备烧录
# create map/bin/hex/uf2 file in addition to ELF.
pico_add_extra_outputs(hello)
```

hello工程的文件如下所示：

```batch
D:\Programs\hello>dir
 驱动器 D 中的卷没有标签。
 卷的序列号是 9A33-48BC

 D:\Programs\hello 的目录

2025/03/03  16:39    <DIR>          .
2025/03/03  16:39    <DIR>          ..
2025/03/03  15:02               623 CMakeLists.txt
2025/03/02  21:44               139 hello.c
2025/02/19  07:55             6,022 pico_sdk_import.cmake
               3 个文件          6,784 字节
               2 个目录 78,325,645,312 可用字节
```

# 编译&烧录

还是在Developer Command Prompt for VS 2022终端中打开项目的文件夹

![](assets/2025-03-03-16-43-05-image.png)

然后敲命令编译：

```batch
REM 新建一个build文件夹用来存放编译时产生的文件
md build
cd build
REM 生成用于NMake Makefiles的Makefile文件
cmake -G "NMake Makefiles" ..
REM 编译并生成uf2文件
nmake
```

当出现这样的提示就说明nmake编译完成了

![](assets/2025-03-03-16-46-47-image.png)

在build目录下就可以看到我们需要的uf2文件了

![](assets/2025-03-03-16-47-43-image.png)

按住`BOOTSEL`连接pico，把uf2文件拖进去就可以编译了。

程序中设置启用的是默认串口，即pico的1、2脚，波特率为115200，连接串口就会看到每隔一秒钟重复发送的`hello, world`。

![](assets/2025-03-03-17-11-42-image.png)

至此，pico开发环境搭建完成了（总算是完了...）
