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

The `$LANG` output like `en_US.UTF-8`. So the `.` is represent the single character.

## Brach Expand
Sometimes the expand may depend on you locale. See Example:

```bash
$echo th{e,a}n
then than
$echo {1..9}
1 2 3 4 5 6 7 8 9
```

