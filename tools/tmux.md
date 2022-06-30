# Tmux简明教程

* Tmux：一个终端复用器，将会话与窗口分离。
  * 窗口：终端窗口。
  * 会话：用户与计算机的一次交互。
* 窗口和会话是绑定的，打开窗口会话开始；关闭窗口会话结束，会话内部的进程页随之终止。

### 基本用法

#### 安装

```shell
sudo apt-get install tmux
```

#### 配置文件

* 该文件帮助tmux在分窗口或分窗格的时候保持现有的工作目录。

```shell
cd ~
# 创建文件.tmux.conf
vim .tmux.conf
# 在文件中键入
bind-key c new-window -c "#{pane_current_path}"
bind-key % split-window -h -c "#{pane_current_path}"
bind-key '"' split-window -c "#{pane_current_path}"
```

#### 前缀键

* Tmux的快捷键需要用前缀键唤起，快捷键仅能在tmux中使用。
* 默认前缀键为`Ctrl+b`，先按下`Ctrl+b`，在按下相应快捷键。
  * 例如，划分上下窗格`Ctrl+b "`需要先按下`Ctrl+b`，松开后按下`Shift+"`。

### 会话管理

#### 新建会话

```shell
# tmux自动给窗口以数字为编号命名，也可以直接为会话起名
tmux new -s <session-name>
```

#### 分离会话

```shell
# 将当前会话与窗口分离
tmux detach
```

* 执行上述命令会退出当前tmux窗口，但其中的会话和进程仍在后台运行。

#### 查看会话

```shell
tmux ls
# or
tmux list-session
```

#### 接入会话

```shell
# session-name可以是编号也可以是会话名
tmux attach -t <session-name>
```

#### 杀死会话

```shell
# session-name可以是编号也可以是会话名
tmux kill-session -t <session-name>
```

#### 切换会话

```shell
# session-name可以是编号也可以是会话名
tmux switch -t <session-name>
```

#### 重命名会话

```shell
# session-name可以是编号也可以是会话名
tmux rename-session -t <old-name> <new-name>
```

#### 会话快捷键

* **`Ctrl+b d`：**分离当前会话。
* **`Ctrl+b s`：**列出所有会话，并可以切换会话。
* **`Ctrl+b $`：**重命名当前会话。

### 窗格操作

#### 划分窗格

```shell
# 划分上下窗格
tmux split-window
# 划分左右窗格
tmux split-window -h
```

#### 移动光标

```shell
# 光标切换到上方窗格
tmux select-pane -U
# 光标切换到下方窗格
tmux select-pane -D
# 光标切换到左边窗格
tmux select-pane -L
# 光标切换到右边窗格
tmux select-pane -R
```

#### 交换窗格位置

```shell
# 当前窗格上(左)移
tmux swap-pane -U
# 当前窗格下（右）移
tmux swap-pane -D
```

#### 窗格快捷键

* **`Ctrl+b %`：**划分左右两个窗格。
* **`Ctrl+b "`：**划分上下两个窗格。
* **`Ctrl+b <arrow-key>`：**光标切换到其他窗格，<arrow-key>是方向键。
* **`Ctrl+b {/}`：**当前窗格与上一个/下一个窗格交换位置。
* **`Ctrl+b Ctrl+o/Alt+o`：**所有窗格向前/向后移动一个位置。
* **`Ctrl+b x`：**关闭当前窗格。
* **`Ctrl+b !`：**将当前窗格拆分为一个独立窗口。
* **`Ctrl+b z`：**当前窗格全屏显示，再使用一次会变回原来大小。
* **`Ctrl+b Ctrl+<arrow-key>`：**用方向键调整窗格大小。
* **`Ctrl+b q`：**显示窗格编号。

### 窗口管理

#### 新建窗口

```shell
# tmux自动编号
tmux new-window
# 指定窗口名称
tmux new-window -n <window-name>
```

#### 切换窗口

```shell
# window-name可以是编号，也可以是名称
tmux select-window -t <window-name>
```

#### 重命名窗口

```shell
# 为当前窗口重命名
tmux rename-window <new-name>
```

#### 窗口快捷键

* **`Ctrl+b c`：**新建窗口。
* **`Ctrl+b p/n`：**切换到上/下一个窗口。
* **`Ctrl+b <number>`：**切换到指定编号的窗口。
* **`Ctrl+b w`：**从列表中选择窗口。
* **`Ctrl+b ,`：**重命名当前窗口。
