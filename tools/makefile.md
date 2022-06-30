## Makefile

进一步细节可参考陈皓大神的博客《跟我一起写makefile》，在github上有人将其整理为网页版与pdf版。

github：[https://github.com/seisman/how-to-write-makefile](https://github.com/seisman/how-to-write-makefile "跟我一起写makefile")

---

> ps：make命令会在当前目录下顺序查找"GNUmakefile", "makefile", "Makefile"文件，一般我们都会采用"Makefile"，方便后续有需要时增加更高优先级的"makefile"。
>
> * 若需指定文件：
>
>   ```shell
>   make -f build.mk
>   ```

**编译（compile）**：保证语法与声明正确。将源文件编译成中间目标文件，Windows下为`.obj`，UNIX下为`.o`，即Object file。

**链接（link）**：链接函数与中间变量，把大量Object file合成执行文件。将中间目标文件打包，Windows下为`.lib`，UNIX下为`.a`。



### makefile基本规则

```makefile
target ... : prerequisites ...
	command
	...
	
# example
program : program.c head.h
	g++ program.cc head.h -o program 
```

**target：**可以是**一个**目标文件、执行文件、label

**prerequisites：**生成target所依赖的文件或target

**command：**该target要执行的命令（shell）

上述代码块为一个文件的依赖关系，target是由prerequisites按照command生成的。makefile通过多个上述代码块，根据多个依赖关系一层层生成`.o`文件或执行文件。

> prerequisites中如果有一个以上的文件比target文件新，则执行command

#### makefile变量

```makefile
objects = main.o kbd.o command.o ...		# 定义变量
$(objects)		# 使用变量
```

#### make自动推导

make看到`.o`文件时，自动将相应`.c`文件添加到依赖关系中。且编译`.c`文件的command也会自动推导。

还可以将多个具有相同`.h`文件的代码块合并。

```makefile
# 自动推导.c文件依赖关系与command
main.o : defs.h
kbd.o : defs.h command.h
files.o : defs.h command.h buffer.h

# 合并相同.h的依赖关系
main.o kbd.o files.o : defs.h
kbd.o files.o : command.h
files.o : buffer.h
```

#### 清空目标文件

```makefile
# 常见写法
clean:
	rm program $(objects)
# 另一种写法
.PHONY : clean				# .PHONY表示clean是伪目标文件：显示指明该目标才生效
clean:
	-rm progrm $(objects)	# - 表示遇到出现问题的文件跳过
```

#### 引用其它makefile

```makefile
include <filename>			# filename可以是文件名、通配符或变量
```

#### 文件搜索（VPATH / vpath）

为了make能在不同的路径下寻找文件，需要指定`VPATH`变量或`vpath`关键字。

```makefile
VPATH = src:../headers			# 目录由 : 分隔，make对其顺序搜索
vpath <pattern> <directories>	# 为符合模式pattern的文件指定搜索目录directories
vpath <pattern>					# 清除符合模式pattern的文件搜索目录
vpath							# 清除所有已设置的文件搜索目录
```

**pattern：**需包含`%`，表示匹配零或若干字符（`%.h`表示所有`.h`结尾的文件）

**directories：**指定pattern文件集的搜索目录

```makefile
# example
vpath %.h ../headers			# make在../headers下搜索所有.h文件
```

#### 伪目标`.PHONY`与同时生成多个可执行文件

伪目标还可以用于使用一次make文件**生成多个可执行文件**。

```makefile
# example
all : prog1 prog2 prog3				# makefile第一个目标是默认目标
.PHONY : all

prog1 : prog1.o utils.o
    cc -o prog1 prog1.o utils.o

prog2 : prog2.o
    cc -o prog2 prog2.o

prog3 : prog3.o sort.o utils.o
    cc -o prog3 prog3.o sort.o utils.o
```

伪目标还能作为依赖使用，功能和**子程序**类似。

```makefile
.PHONY : cleanall cleanobj cleandiff

cleanall : cleanobj cleandiff
    rm program

cleanobj :
    rm *.o

cleandiff :
    rm *.diff
```

#### 自动化变量

| $<   | 第一个依赖文件                   |
| ---- | -------------------------------- |
| $@   | 目标集合，依次取出目标并执行命令 |

#### 静态模式

静态模式非常强大，可以大大减少重复的代码量。

```makefile
<targets ...> : <target-pattern> : <prereq-pattern ...>
	<command>
	...	
# target: 目标的集合
# target-pattern：target的模式（%.o表示target中都是.o）
# prereq-pattern：target的依赖模式（%.c表示将符合pattern的target的后缀改为.c）

# example
$(objects) : %.o : %.c		# targets为变量objects中的.o文件，依赖为targets后缀改为.c
	$(g++) -c $< -o $@		# 依次将目标编译
```

#### 自动生成依赖

编译器可以通过源文件自动生成依赖关系：

```shell
g++ -MM program.cc		# -M会将标准库也包含进来
```

为了使makefile也能自动生成依赖关系，GNU建议将每一个源文件`.c`自动生成依赖关系保存在同名`.d`文件中。

产生`.d`文件的模式规则：

```makefile
%.d: %.c
    @set -e; rm -f $@; \									# 删除原有.d
    $(CC) -M $(CPPFLAGS) $< > $@.$$$$; \					# 生成.d
    sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@; \	# sed替换
    rm -f $@.$$$$											# 删除临时文件
# 把依赖关系 main.o: main.c defs.h 转变为 main.o main.d: main.c defs.h
-include xxx.d		# 将自动生成依赖导入makefile
```

