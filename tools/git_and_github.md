## git 和 github 常用操作

### git 的基本使用方式

#### 创建 git 仓库

``` sh
git init
```

在需要创建仓库的目录下运行该指令，该目录下会生成` .git`文件夹。修改该文件夹会破坏 git 仓库。

#### 设置用户名或邮件

```shell
git config --global user.name "your_name" 				# 设置用户名
git config --global user.email "your_email@mail.com"	# 设置邮箱
git config -l		# 查看配置参数
```

git 的用户名是提交者的信息，与 github 账号并非绑定关系。但 git 用户与 github 账号不同的情况下，在 github 上提交代码时，github 记录的提交者将会是 git 用户。

#### 添加文件至仓库

```shell
git add file.txt
```

#### 提交文件至仓库

```shell
git commit -m "do something"	# "do something"描述该次提交做了什么
```

#### 查看仓库状态

``` shell
git status
```

显示当前修改后的未添加进仓库的文件，或添加至仓库但还未提交的文件。

#### 版本改动

```shell
git log			# 查看曾经commit的版本，HEAD表示当前版本
git reset --hard HEAD^		# 版本回退至上一版本
git reset --hard xxxx		# xxxx可填版本号前几位，git会自动查找
git reflog		# 查看之前的命令，可以在其中找到回退前的版本号
```

#### 撤销修改

```shell
git checkout -- readme.txt		# 丢弃工作区修改
								# 让readme.txt回到最近一次commit或add的状态
git reset HEAD readme.txt			# 把缓存区的修改撤销，放回工作区
```

#### 删除文件

```shell
git rm readme.txt		# 删除文件后更新缓存区
```



### github 的基本使用方式

#### 配置SSH Key（新主机需要配置）

为了使用远程仓库提交代码时不需要每次都输入 github 账号密码，每一台新主机都需要配置 ssh key。

```shell
ls ~/.ssh			# 查看该目录下是否有 id_rsa 和 id_rsa.pub，若有则无需生成
ssh-keygen -t rsa -C "youremail@example.com"	# 生成上述文件，选择默认值即可
cat ~/.ssh/id_rsa.pub		# id_rsa为私钥，id_rsa.pub为公钥
```

下一步，登陆 github，在设置界面选择添加 SSH Key，将 id_rsa.pub 拷贝进去。名字可以随便取。

#### 添加与更新远程仓库

在 github 上创建一个空远程仓库，在本地仓库目录下运行下面命令。

```shell
git remote add origin git@github.com:llipa/Notes.git	# 将本地仓库与远程仓库关联起来
git push -u origin master		# 将本地仓库推送到远程仓库
								# -u 将本地 master 分支和远程 master 分支关联，后续可省略
git push origin master			# 将本地 master分支的最新修改推送至 github
```

#### 更新本地仓库

```shell
# 该操作会覆盖本地未提交代码，请在保证本地无重要修改时再使用
git pull origin master			# 直接将远程仓库代码

# 保险的更新方式
git fetch origin master:temp		# 从远程仓库读取代码并创建一个名为temp的分支
git diff temp				# 比较temp分支和master分支的差异
git merge temp				# 如果上述操作觉得没问题，即可合并temp分支
git branch -d temp			# 删除temp分支
```
>关于分支管理的原理与操作可以参考[廖雪峰的博客](https://www.liaoxuefeng.com/wiki/896043488029600/896954848507552)。
#### 解除绑定远程库

```shell
git remote -v				# 查看远程库信息
git remote rm origin		# 根据第一列的名字解除本地与远程绑定关系
```

要删除远程库，需要在 github 上删除库。

#### Fork

当需要在别人的库的基础上开发，并需要在 github 上进行版本控制时，需要在 github 上 fork 别人的库，将其克隆到自己的账号下，然后从自己的账号下 clone。否则将无法推送修改。

### 分支管理

* git中有一个`HEAD`指针指向当前分支。
* `master`指向主分支。

#### 查看/创建/切换分支

```shell
# 查看分支
git branch
# 创建分支
git branch dev
# 切换分支
git switch dev
git checkout dev
# 创建并切换分支
git checkout -b dev
```

#### 合并分支

```shell
# 当master和分支不存在冲突时，可以“快速合并”，否则会报错
git merge dev
```

#### 删除分支

```shell
git branch -d dev
```

### 标签操作

* git中的标签，指的是某个分支某个特定时间点的状态。通过标签，可以方便的切换到标记时的状态。

```shell
# 列出已有标签
git tag
# 创建标签
git tag [标签名]
# 把标签推送至远程仓库
git push origin [标签名]
# 检出标签（会创建新分支）
git checkout -b [branch] [标签名]
```
