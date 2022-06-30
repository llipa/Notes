# 终端翻译软件Translate-shell
* [Translate-shell](https://github.com/soimort/translate-shell)是一个Linux下好用的翻译软件，可以在终端下运行。
* Translate-shell的默认翻译器是Google Translate， 也可以更改为Bing等。

## 简单使用说明

### 安装

```shell
wget git.io/trans
chmod +x ./trans
# 为了能直接在终端下运行trans，还需要将其放置在环境变量的目录下
sudo mv trans /usr/bin/
```

* `trans`可能会缺少`gawk`包，因此还需要`sudo apt-get install gawk`。

### 普通使用

```shell
# 直接查询
trans 'Saluton, Mondo!'
# 查询简单结果-b(-brief)
trans -b 'Saluton, Mondo!'
# 翻译为一种/多种语言（中文/中文+日文）
trans :zh word
trans :zh+ja word
# 将某种语言翻译为默认语言（英语）
trans zh: 手纸
# 翻译短语或句子需要使用""或''
trans :zh "To-morrow, and to-morrow, and to-morrow,"
```

* `trans`还提供交互式窗口：

```shell
# -shell表示交互式窗口，其余选项可选
trans -shell -b en:zh
```

### 骚操作

* `trans`还能帮助检测语种：

```shell
trans -id 言葉
```

* 翻译文件或者网页：

```shell
# 翻译文件
trans :zh ~/text.txt
# 翻译网页，-broswer选项可选，默认为默认浏览器（firefox）
trans -broswer google-chrome :en http://www.baidu.com
```

* 甚至还能读出来：

```shell
# 读翻译结果，-p(-play)
trans -b -p :ja "Saluton, Mondo"
# 读原文，-sp(-speak)
trans -sp "你好，世界"
```

