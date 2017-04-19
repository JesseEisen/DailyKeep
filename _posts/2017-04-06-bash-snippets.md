---
title: bash snippet highlight
updated: 2016-04-06 18:00
---

##### 1. cleanup when exit

```bash
cleanup(){
	#...	
}
trap cleanup EXIT
```

在脚本退出的时候，trap会调用cleanup函数来进行清理


##### 2. Easy way to generate usage info

```bash
#/ Usage: add <args> [option]
#/ this is a comment for descripte this script
usage() {
	grep '^#/' "$0" | cut -c4-
	exit 0	
}

expr "$*" : ".*--help" > /dev/null && usage
```

expr 命令会检查字符串中是否包含后面的匹配模式，如果有的话则执行usage。这里面使用grep和cut来去掉前面的注释标签

##### 3. custom your log 

```bash
readonly LOG_FILE="/tmp/$(basename "$0").log"
info()	  { echo "[INFO]    $@" | tee -a "$LOG_FILE" >&2; }
warning() { echo "[WARNING] $@" | tee -a "$LOG_FILE" >&2; }
error()   { echo "[ERROR]   $@" | tee -a "$LOG_FILE" >&2; }
fatal()   { echo "[FATAL]   $@" | tee -a "$LOG_FILE" >&2;  exit 1;}
```

##### 4. some ways to parse arguments

+ straight bash space separated

```bash
while [[ $# -gt 1 ]]
do
key="$1"

case $key in 
	-e|--extension)
	EXTENSION="$2"
	shift
	;;
	-s|--searchpath)
	SEARCHPATH="$2"
	shift
	;;
	-l | --lib)
	LIBPATH="$2"
	shift
	;;
	--default)
	DEFAULT=YES
	;;
	*)
	#unknow option
	;;
esac
shift
done

echo FILE_EXTENSION  = "${EXTENSION}"
echo SEARCH PATH     = "${SEARCHPATH}"
echo LIBRARY PATH    = "${LIBPATH}"
echo "Number files in SEARCH PATH with EXTENSION:" $(ls -1 "${SEARCHPATH}"/*."${EXTENSION}" | wc -l)
if [[ -n $1 ]]; then
	echo "Last line of file specified as non-opt/last argument:"
	tail -1 $1
fi

```

+ straight bash equals separated

```
for i in "$@"
do
case $i in
	-e=*|--extension=*)
	EXTENSION="${i#*=}"
	shift # past argument=value
	;;
	-s=*|--searchpath=*)
	SEARCHPATH="${i#*=}"
	shift # past argument=value
	;;
	-l=*|--lib=*)
	LIBPATH="${i#*=}"
	shift # past argument=value
	;;
	--default)
	DEFAULT=YES
	shift # past argument with no value
	;;
	*)
		#unknown option
	;;
esac
done
echo "FILE EXTENSION  = ${EXTENSION}"
echo "SEARCH PATH     = ${SEARCHPATH}"
echo "LIBRARY PATH    = ${LIBPATH}"
echo "Number files in SEARCH PATH with EXTENSION:" $(ls -1 "${SEARCHPATH}"/*."${EXTENSION}" | wc -l)
if [[ -n $1 ]]; then
	echo "Last line of file specified as non-opt/last argument:"
	tail -1 $1
fi
```

+ getopts

never use getopt, because cannot handle empty arguments strings, or arguments with embedded whitespace.

```
#!/bin/sh

# A POSIX variable
OPTIND=1         # Reset in case getopts has been used previously in the shell.

# Initialize our own variables:
output_file=""
verbose=0

while getopts "h?vf:" opt; do
	case "$opt" in
	h|\?)
		show_help
		exit 0
	;;
	v)  verbose=1
	;;
	f)  output_file=$OPTARG
	;;
esac
done

shift $((OPTIND-1))

	[ "$1" = "--" ] && shift

	echo "verbose=$verbose, output_file='$output_file', Leftovers: $@"
```

Refer: 
+ [parse arguments in bash](http://stackoverflow.com/a/14203146/4168837)   
+ [Manipulating strings](http://tldp.org/LDP/abs/html/string-manipulation.html)


##### 5.Prompt for yes and no 
脚本中需要让用户确认是否继续，有以下一些简单的脚本事例

```bash
echo -n "Is this a good script(y/n)? "
read answer
if echo "$answer" | grep -iq "^y"; then
	echo Yes
else
	echo No
fi
```

使用read和case相互搭配的方式：

```bash
read -p "Is this a good question (y/n)? " answer
case ${answer:0:1} in
	y|Y) 
		echo Yes
	;;
	*  )
		echo No
	;;
esac
```

这个需要在输入后按下enter进行确认，如果不像按下enter直接确认的话，可以有如下的方式：

```bash
echo -n "Is this a good script(y/n)? "
old_stty_cfg=$(stty -g)
stty raw -echo; answer=$(head -c 1); stty $old_stty_cfg
if echo "$answer" | grep -iq "^y"; then
	echo Yes
else
	echo No
fi
```
这块使用的是stty进行设置，不过你会注意到，上述的这个脚本中，有一个小问题，就是当不输入以y开头的都视为错误，这个在逻辑上没问题，但是如果我们想让输入只能是y和n的话，可以做一个检查。

```bash
answer=$( while ! head -c 1 | grep -i '[ny]'; do true ;done )
```

有时候我们会发现在安装某些软件的时候，会调转到一个对话框，这些是通过一系列的专用的工具实现的。如下：

```bash
if whiptail --yesno "Is this a good script" 20 60; then
	echo Yes
else
	echo No
fi
```

同时如果安装了`dialog`、`gdialog` 、`kdialog` 可以将`whiptail`替换成相应的工具。


##### 6. Using  readline's history

```bash
set -i 
HISTFILE=~/.myscript.history
history -c 
history -r 

myread() {
	read -e -p '> '	 $1
	history -s ${!1}
}

trap 'history -a; exit' 0 1 2 3 6

while myread line;do
	case ${line%% *} in
		exit ) break ;; 
		*    ) echo "Doing something with '$line'" ;;
	easc
done
```

这个会在每次启动的时候使用 `up`,`down`,和`ctrl+r`等进行搜索 。

注： ${!1} 是将变量进行扩展，比如如下的例子：

```bash
$ animal=cat
$ echo $animal
cat
$ cat=tabby
$ echo $cat
tabby
$ echo ${!animal}
tabby
```

同时`eval` 也可以做到这个,即`eval \$$animal`,不过这个操作直接返回的是结果，因此不适合作为参数传入。不建议使用eval。

##### 8. Set color for output

使用`tput`来修改输出的颜色。 下面可以打印出一个颜色表，可以通过这个表来进行：

```bash
for (( i = 0; i < 8; i++ )); do
	for (( j = 0; j < 8; j++ )); do
		printf "$(tput setab $i)$(tput setaf $j)(b=$i, f=$j)$(tput sgr0)\n"
	done
done
```

下面可以是ANSI的转义码：

```bash
Black        0;30     Dark Gray     1;30
Red          0;31     Light Red     1;31
Green        0;32     Light Green   1;32
Brown/Orange 0;33     Yellow        1;33
Blue         0;34     Light Blue    1;34
Purple       0;35     Light Purple  1;35
Cyan         0;36     Light Cyan    1;36
Light Gray   0;37     White         1;37
```

我们可以这样使用：

```bash
RED='\033[0;31m'
NC='\033[0m'
printf "I ${RED} LOVE ${NC} STACK OVERFLOW\n"
```


##### 9. Read content of a file

在bash中读取一个文件，可以通过while循环，比如最简单的读取如下：

```bash
while read line; do
	echo $line
done < input.txt
```

如果遇到在循环体内又要从标准输入中读取内容的时候，可以使用如下方式：

```bash
while read -u 10 line; do
	...
done 10<input.txt
```

read的参数`-u`是用来指定一个文件描述符，这个描述符只要不等于(0,1,2)就行。




