# gdb简明教程

### 调试准备

* 要使得程序可调试，应在编译时加入`-g`：

```shell
g++ main.cpp -g -Wall -Werror -o main
```

* `-Wall`选项可以在启用所有警告的情况下进行编译。
* `-Werror`选项让编译器将警告视为错误。

### 常用指令

* 对某个可执行文件进行调试：

```shell
gdb <excutable-name>
```

* **启动：**

```shell
# 显示代码
list
# 运行程序
run
# 退出
quit
```

* **breakpoints：**

```shell
# 设置breakpoints
break <line-number>
# 清除breakpoints
clear <line-number>
delete <breakpoints-number>
```

* **步进：**

```shell
# 步进：step会进入函数中，next会保持在当前上下文
step/next
```

* **变量操作：**

```shell
# 查看变量
print <var-name>
# 改变变量
set <var-name>
# 设置watchpoints
watch <var-name>
# 清除watchpoints
delete <watchpoints-number>
```

> `delete`指令可以依照序号删除breakpoints和watchpoints，不指定序号则清除所有breakpoints和watchpoints。

### gdb自带代码模式

* 在终端启动：

```shell
gdb -tui program
```

* 在gdb内启动和终止

```shell
# 启动代码模式
tui enable
# 终止代码模式
tui disable
```

