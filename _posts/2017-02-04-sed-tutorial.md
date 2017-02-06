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

### 经常使用的命令

+  q [exit-code]  退出sed，不再执行任何命令。

```
$ seq 3 | sed 2q
1
2
```
这个命令只接受single address， 同时这个命令会将pattern space中的内容输出，如果使用`-n`的话，则不会输出什么。
GNU sed 提供了一个Q的选项，功能和q类似，但是不输出当前行的pattern space。

+ p  打印pattern space中的内容

```
$ seq 5 | sed -n 3,5p
3
4
5
```

这个命令一般和`-n`搭配使用，接受一个单个的地址作为参数，也可以是一个地址范围。

+ n 如果auto-print 没有被关闭，则打印出pattern space，然后用下一个输入替换pattern space

```
$ seq 6 | sed 'n;n;s/./x/'
1
2
x
4
5
x
```

n命令一般被用来跳过某些行，同时GUN的sed还有一个扩展。 `first~step` 这样的快捷方式来实现上面的这个功能。

+ { }  作为一个一组命令

```
$ seq 5 | seq '3{s/3/x/}'
1
2
x
4
5
```

用`{}`包裹的部分，可以是一组命令，可以作用在单个地址上，可以比较的灵活一点。

### 不经常使用的命令

+ y/source-char/dest-char/  这个命令和tr类似
+ a text  在当前行后面添加text。 可以使用 `\` 来进行多行的添加
+ i text  在当前行的前面插入text  可以使用`\`来进行多行的添加
+ c text  修改当前行或者是多行的文本。

```
$seq 10 | sed '2,9c hello'
1
hello
9
```
+ = 输出当前行的行号


## 选择行

+ line number
+ line range
+ regex  /pattern/
+ !  表示除去指定的行号或者是行的范围或者是匹配的pattern的行

```
sed '/hello/s/H/World/g'  #将匹配到hello行的H替换成World
sed '1,4s/H/World/g'      #将1到4行的H替换成World
sed '2,5!s/H/World/g'     #除了2到5外，将有H的替换成World
```
