## CMake基础知识
具体教程可参见[CGold的CMake教程](https://cgold.readthedocs.io/en/latest/index.html)。

**优点：**

CMake是一款跨平台开发C++项目的工具，可以有效避免手动编译链接不同平台下的项目。

CMake通过用户设计的**CMakeLists.txt文件**产生不同平台下的**工程文件**（如makefile），然后用户即可使用对应平台下的编译器进行编译链接。

>**安装：**
>
>* 在Ununtu下使用`sudo apt-get install cmake`即可安装
>* 也可以在[CMake官网](https://cmake.org/install/)按照指导下载并安装

### CMake使用方式

> * 内部构建：直接在源文件同级目录下生成编译输出文件，不推荐使用
> * **外部构建：**将编译输出文件和源文件放在不同的目录中

在项目的**每一个**目录下都需要有CMakeLists.txt文件。

`cmake PATH`命令会生成Makefile文件，PATH是顶层CMakeLists.txt所在目录。

```shell
# 外部构建，CMake输出文件会放在创建的文件夹中
mkdir build
cd build
cmake ..
make

# cmake还可以使用-S指定源文件目录和-B目标文件目录
cmake -S . -B build
```



### CMakeLists.txt常用指令

补充常用指令见[CMake简明教程](http://www.wang-hj.cn/?p=2629)

> CMake指令的大小写是等价的

##### cmake_minimum_required

```cmake
cmake_minimum_required(VERSION 2.8)			# 指定cmake的最低版本
```

##### project

```cmake
project(projectname [CXX] [C] [java])		# 指定工程名字，可指定多个支持语言
```

project隐式定义两个变量：

\<projectname\>\_BINARY_DIR和\<projectname\>\_SOURCE_DIR，如果改变工程名字则这两个变量名会改变。

为了更方便的调用这两个变量，cmake会生成两个预定义变量：

PROJECT_BINARY_DIR和PROJECT_SOURCE_DIR，分别代表上述两个变量。

##### set

```cmake
set(variablename value1 value2 ...)			# 定义变量名
set(SRC hello.cpp sayhello.cpp)
${SRC}		# 取变量名
```

##### add_executable

```cmake
add_executable(programname source1 source2 ...)		# 生成可执行文件
```

> programname和projectname并没有关系

##### add_subdirectory

```cmake
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])	# 添加并构建子目录
add_subdirectory(src bin)
```

source_dir：指定子目录，子目录中需要有CMakeLists.txt文件和代码文件。

binary_dir：指定一个source_dir下目录，用于存放中间二进制和目标二进制文件。默认为source_dir。

EXCLUDE_FROM_ALL：父目录CMakeLists.txt不会构建子目录的目标文件，需要在子目录下显式构建。

##### add_library

```cmake
# 生成库文件
add_library(libname [SHARED|STATIC|MODULE] [EXCLUDE_FROM_ALL] source1 ...)
add_library(hello SHARED hello.cpp)		# 自动产生libhello.so
```

SHARED：动态库

STATIC：静态库

source：源文件

> * 静态库：.a或.lib；编译时会自动整合到目标程序中，编译后可执行文件独立运行
> * 动态库：.so或.dll；编译时不放在连接的目标程序中，可执行文件无法单独运行

##### include_directories

```cmake
include_directories([AFTER|BEFORE][SYSTEM] dir1 dir2 ...)	# 添加头文件搜索路径
```

##### link_directories

```cmake
link_directories(dir1 dir2 ...)		# 添加库文件搜索路径
```

##### target_link_libraries

```cmake
target_link_libraries(target library1 library2 ...)		# 为target添加需要链接的库
```

##### aux_source_directory

```cmake
aux_source_directory(dir varname)		# 将dir目录下的源文件保存在变量varname中
aux_source_directory(. SCR)
```

##### message

```cmake
message([关键字] "message")				# 显示信息
# 可选关键字：
# (无) = 重要信息
# STATUS = 非重要信息
# WARNING = CMake警告，会继续执行
# AUTHOR_WARNING = CMake开发者警告，会继续执行
# SEND_ERROR = 错误，继续执行，并跳过生成步骤
# FATAL_ERROR = 错误，终止所有处理过程
```

##### install

```cmake
install(TARGETS target1 target2 ... [DESTINATION dir])	# 指定安装目录dir
# TARGETS:
# FILES 安装文件
# PROGRAMS 安装脚本
# DIRECTORY dir 安装目录dir下文件
```

### CMakeLists.txt与cmake

##### CMakeLists.txt与cmake与make

在写好CMakeLists.txt并执行cmake后，生成的构建系统可以自动检测CMakeLists.txt的改变，并做出相应的更新（CMakeLists..txt会作为makefile的依赖文件存在）。因此一般情况下不需要反复执行cmake，仅需要执行make重新编译即可。

#### 多目录下的CMake

在多目录下编写CMakeLists.txt可以采用两种方式：

1. 直接采用源文件地址生成工程文件
2. 将源文件通过编译生成库文件后，再将他们链接到可执行文件中

##### 采用源文件地址

```shell
.						# 目录树
├── build
├── CMakeLists.txt
├── include
│   └── hellomachine.h
├── program.cpp
└── scr
    └── name.cpp
```

如果直接采用源文件地址，则不需要添加并构建子目录，因此只需要主目录的CMakeLists.txt。

```cmake
project(program CXX)
# 虽然不需要添加并构建子目录，但添加头文件搜索路径还是有必要的
include_directories(include)
# 直接使用源文件的地址来生成可执行文件
# 在项目文件较少的情况下可以使用这种方式
add_executable(program program.cpp scr/name.cpp)
```

##### 生成库文件

```shell
.						# 目录树
├── build
├── CMakeLists.txt
├── include
│   └── hellomachine.h
├── program.cpp
├── README.md
└── scr
    ├── CMakeLists.txt
    └── name.cpp

```

当`scr`文件夹下的结构较为复杂，直接使用地址会造成后续更新十分繁琐。这时候就可以采用`add_subdirectories`，通过添加子目录，然后通过子目录下的CMakeLists.txt文件构建子目录（比如将该目录下的源文件生成库文件）。

```cmake
# 主目录下的CMakeLists.txt
project(program CXX)
# 添加头文件搜索路径
include_directories(include)
# 添加子目录，根据子目录的CMakeLists.txt构建
add_subdirectory(scr bin)
# 生成可执行文件
add_executable(program program.cpp)
# 将子目录构建生成的库文件与可执行文件链接
target_link_libraries(program name)
```

```cmake
# 子目录下的CMakeLists.txt
# 将该目录下文件打包成静态库文件
aux_source_directory(. SCR)
add_library(name STATIC ${SCR})
```
