---
title: Bash Shell Synatx OverView
updated: 2016-01-01  16:21
---

## Parameters

+ **Array**: `declare -a variable` 
+ **Associative array**: `declare -A variable`
+ **Integer**: `declare -i variable`
+ **Read Only**: `declare -r variable`
+ **Export**: `declare -x variable`

> It is rare to use `declare -i`, We often use the `(())` or `let`


## Parameter Expansion

+ ${paramter:-word} **Use Default Value**

```bash
$ echo ${hello:-"world"} 
$ world
$ echo "$hello"
                   #hello is null, not assign
$ hello="Hello"
$ echo ${hello:-"world"}
Hello
```
+ ${parameter:=word} **Assign Default Value**

```bash
$ echo ${hello:="world"} 
world
$ echo "$hello"
world                
$ hello="Hello"
$ echo ${hello:="world"}
Hello
```

+ ${parameter:+word}  **Use Alternate Value**

```bash
$ echo ${hello:+"world"} 
                      #nothing 
$ echo "$hello"
                      #still nothing
$ hello="Hello"
$ echo ${hello:+"world"}
world
$ echo "$hello"
Hello
```

+ {parameter:offset:length} **Substring Expansion**

```bash
$ hello="HELLO"
$ echo ${hello:1:2}
EL
```

+ ${#parameter}  The length in characters of value in `parameter`
+ ${parameter#pattern} Match against the **beginning** of `parameter`
+ ${parameter##pattern}  The longest match is *deleted*

```bash
$ path=/home/username/file.txt
$ echo ${path#/}
home/username/file.txt
$ echo ${path#*e}
username/file.txt
# longest match
$ echo ${path##*/}
file.txt
$ echo ${path##*e}
.txt
```

+ ${parameter%pattern} against the **end** of the `parameter`
+ ${parameter%%pattern} longest match is *deleted*

```bash
$ path=/home/username/file.txt
$ echo ${path%/*}
/home/username
$ echo ${path%e*}
/home/username/fil
# longest match
$ echo ${path%%/*}
                 # empty, delete all
$ echo ${path%%e*}
/hom
```

+ ${parameter/pat/string}  substitute pattern with string 
+ ${parameter//pat/string} substitute all pattern

```bash
$ path=/home/username/file.txt
$ echo ${path/e/E}
/homE/username/file.txt
$ echo ${path//e/E}
/homE/usErnamE/filE.txt
```
+ ${parameter/#pat/string} 
+ ${parameter/%pat/string}

```bash
$ path=/home/username/file.txt
$ echo ${path/#*e/E}
E.txt
$ echo ${path/%e*/E}
homE
# add prefix to array
$ A=(hello world yeah)
$ echo ${A/#/R}
Rhello Rworld Ryeah
```

## Glob Patterns

+ `*`: matches any string, including the null string
+ `?`: matches any single character
+ `[..]`: matches any **one of** the enclosed character

```bash
if [[ $filename = *.txt ]]; then 
  echo "$filename is a txt"
fi
```

## Extended Globs

First we need open it. Use `shopt -s extglob`

+ ?(list)  matches **zero or one** occurrence of the given patterns
+ +(list)  matches **one or more** occurrence of the given patterns
+ *(list)  matches **zero or more** occurrence of the given patterns
+ @(list)  matches **one** of the given patterns
+ !(list)  matches **anything but the given patterns**

The list inside the parenthness is a list of globs or extended globs separated by `|`.

```bash
echo !(*jpg | *bmp)
```
Another example:

```bash
if [[ $var == @(foo|bar|more) ]]; then
      ...
fi
```

## Regular Expressions

It can only be match the pattern not for filename match. Bash use the `ERE` dialect. Bash
support the `=~` to do a regular expression

```bash
langRegex='(..)_(..)'
$ if [[ $LANG =~ $langRegex ]]
then
    echo "Your country code (ISO 3166-1-alpha-2) is ${BASH_REMATCH[2]}."
    echo "Your language code (ISO 639-1) is ${BASH_REMATCH[1]}."
else
    echo "Your locale was not recognised"
fi
```

Another example of regular expressions:

```
if [[ $var =~ @(foo|bar|more) ]]; then
	...
fi
```

The `$LANG` output like `en_US.UTF-8`. So the `.` is represent the single character.

## Brace Expand
Sometimes the expand may depend on you locale. See Example:

```bash
$echo th{e,a}n
then than
$echo {1..9}
1 2 3 4 5 6 7 8 9
```

## Tetes and conditionals

> You should make sure that your scripts always return a non-zero exit code if something unexpected happened in their execution. You can do this with `exit` builtin

Sometimes we can use the control operators between two commands, and **they are used to control whether the second command should be executed depending on the succes of the first.**

That provide us a way to do the simple error check.

```
$ rm -rf somefile || echo "can not remove the file"
```

if `rm` get wrong, The echo statement will exectue. But do not use too much conditional execution. It will makes the script more difficult to understand.

## Grouping Statements

Grouping Statemets use the `{...}` to surround the code. If we use the `conditional execution`. That maybe we need to use the `grouping statments`. For instance:

```bash
grep -q hello "$target" && ! grep -q world "$target" && rm "$target" || echo "can not delete file"
```

If the first grep command get failed. the `echo` will print `con not delete file`. That is not we want, so we need make the rm and echo be a grouping statement. change to that:

```bash
grep -q hello "$target" && ! grep -q world "$target" && {rm "$target" || echo "can not delete file"}
```

One more common use of the `grouping statements` is the error handle.

```bash
cd "$dir" || {echo "please check the "$dir" >&2; exit 1;}
```


## Conditional Blocks 

Styles of `if`. 

```bash
if commands
the other commands
fi

if commands
then
   other commands
fi

if commands; then
   other commands
fi
```
The first such command is `test`(or known as [). And the advanced version is `[[`. The later is more versatility.

You don't need an `if` when use `[`. It just take some paramters and the last parameter is `]`. 

```bash
$ [ "$myname" = "$yourname ]
```

`[[` takes some features than `[`. It support the `patterns`. For instance:

```
$[[ $filename = *.png ]] && echo "the file seems a picture"
```


