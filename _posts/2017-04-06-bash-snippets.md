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

#### 4. some ways to parse arguments

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

```bash
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

```bash
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



