---
title: Sed Tutorials
updated: 2017-02-04  18:00
---

## s command
用来进行文本替换，基本的语法是`s/regexp/replacement/flags`.

replacement部分可以使用`\n`(n表示数字，1~9)引用，这个代表的是第n个用`\(`和`\)`包裹的部分。同时replacment也可以是转义过的`&`, 这个符号用来表示整个匹配的部分。

GUN sed还提供了一些用反斜杠和字母组合起来的序列。 比如`\L \l`,`\U \u`,`\E`等，简单的说：小写的字母只会对下一个字母有作用，大写字母会对之后的所有字符都有作用，除非有`\E`。

```sed
echo 'a-b-' | sed 's/\(b\?\)-/x\u\1/g' -  # 结果： axxB
echo 'a-b-' | sed 's/\(b\?\)-/\u\1x/g' -  # 结果： aXBx
echo 'a-b-' | sed 's/(\b\?\)-/\U\1x/g' -  # 结果： aXBX
```
上面的几个例子则可以很清楚的说明了这个用法。

### flag
+ g  全局替换
+ p  打印新的模式空间
+ w  file-name  将替换后的结果写入文件
+ e  这个命令允许用pipe将输入放到pattern space中，如果还有替换的话，则还会再用pattern sapce中内容进行替换。
+ I、i  不区分大小写
+ M、m 这两个是用在pattern的多行情况下，将`^`和`$`看作是在newline之后的空字符串和在newline之前的空字符串。

```
针对e和M 进一步的说明：

$ ls
download document a test
$ echo "Ls" | sed -e 's/L/l/ge' -e 's/a/A/g'
download
document
A
test

添加上e标签后，sed会执行替换后的命令（命令需要是可以执行的）。然后将命令的输入放到pattern space中，进行后面的操作。

M这个命令可以有如下的对比：

$ seq 3 | sed 'N;s/$/<end>/g'
1
2<end>
3
$ seq 3 | sed 'N;s/$/<end/Mg>'
1<end>
2<end>
3

有了M后会针对多行进行一个操作。
```
