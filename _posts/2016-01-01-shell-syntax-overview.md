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

$ echo ${var-0}  #a slightly different notation
0
$ echo $var
                 #var is empty
$ 
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

### Some operators test support

+ `-z STRING` : True if the string is empty
+ `-n STRING` : True if the string is not empty
+ `-e FILE`   : True if file exists
+ `-f FILE`   : True if file is a regular file

String Operators:

+ `STRING = STRING` 
+ `STRING != STRING` 
+ `STRING < STRING` 
+ `STRING > STRING`

Also there are some useful operator that supported only by `[[`.Not string comparison like with `[`, but pattern matching is performed   

+ STRING = PATTERN    
  True if the string matches the glob pattern.
+ STRING != PATTERN    
  True if the string does not match the glob pattern.   
+ (EXPR)   
  Parentheses can be used to change the evaluation precedence.   
+ EXPR && EXPR   
+ EXPR || EXPR

> Whenever you're making a Bash script, you should always use [[ rather than [.
> Whenever you're making a shell script, which may end up being used in an environment where Bash is not available, you should use [

### Conditional Loops(while, until and for)

+ while 

```bash
while True; do
  echo "Infinite loop"
done

#another
while ! ping -c 1 -W 1 1.1.1.1; do
  echo "still waiting for 1.1.1.1"
done
```

+ for

```
for ((i=10; i>0;i--));do
  echo "$i empty cans of beer"
done

for i in {10..1}
do echo "$i empty cans of beer"
done
```

+ until  
  This is barely ever used, if only because it is pretty much exactly the same as `while !...`

### Choices(case and select)

+ case 

The general framework is: 

```bash
case $xxx in
	xx) 
	    xxxx
		;;
	xx)
	    xxxx
		;;
	xx) 
		xxxx
		;;
esac
```
Each choice in a `case` statement consists of a pattern, a right parenthesi, a block of code that is to executed if the string matches one of those patterns, and two semi-colons to denote the end of the block of code. A left parenthesis can be added to the left of the pattern. Using `;&` instead of `;;` will grant you the ability to fall-through the `case` matching in bash,zsh and ksh.

Therefore, we can use the `*` pattern in the end to match any case that has not been caught by the other choices.

+ select

This statement smells like a loop and is a convenience statement for generating a menu of choices that the user can choose from

```bash
select choice in Apple Pears Crisps Lemons Kiwis; do
 if [[ $choice = Crisps ]]; then 
   echo "Crisps are not fruit"; break; fi
 echo "Errr... no. Try again."
done
```

## Arrays

### Creating Array

There are several ways you can create or fill your array with data. The easiest way to create a simple array with data is by using the `=()` syntax:

```bash
names=("jesse" "bob" "$username")
#or
names=([0]="jesse" [2]="bob" [30]="peter")
#or 
names[0]="jesse"
```


