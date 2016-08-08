---
title: Awk Synatx OverView
updated: 2016-02-03  16:21
---

## 1. Patterns

+ BEGIN { statements }    
**The statements are executed once before any input has been read**
+ END { statements }    
**The statements are executed once after all input has been read**
+ expression { statements }    
**The statements are executed at each iuput line where the `expression` is true**
+ /regular express/ { statements }     
**The statements are executed at each input line contains a string matched by the `regular expression`**
+ compound pattern { statements }     
**A compound pattern combines expression with &&, ||, ! and parenthese**
+ pattern1, pattern2 { statements }     
**A range pattern matches each input line from a line matched by `pattern1` to the next line matched by `pattern2`**

### BEGIN and END

+ The statement in ` BEGIN` action are executed before awk reads any input.
+ If there is more tha one BEGIN, the associated actions are **executed in the order** in which they appear in the program
+ The statement in `END` action are executed after all input has been read.
+ multiple `END` do the similarly like `BEGIN`

One common use of `BEGIN` action is to change the default way that input lines are split inti files. The built-in variable is `FS`.


### Expressions

Unlike many language, awk also has expressions for describing operations on strings. 

The string `""`, which contains no characters, is called the `null string`.

Any expression can be used as an operand of any operator. If an expression has a numeric value but an operator requires a string value, the numeric value is automatically transformed into a string. Similary, a string is converted into a number when a operator demands a numberic value.

There are some comparsion operators, both the `number` and `string` can use them.

+ `<`   less than 
+ `<=`  less than or equal to
+ `==`  equal to
+ `!=`  not equal to
+ `>=`  greater than or equal to
+ `>`   greater than
+ `~`   matched by
+ `!~`  not matched by

Here are some examples:

```bash
"Canada" < "China"   # string compare
$2/$3 >= 0.5         # number compare
$0 >= "M"            # string compare
```

### String-matching 

Here is the syntax of string-matching patterns. Notice that:**blanks are significant within regular expression**

+ `/regexpr/`     
Matches when the current input line contains a substring matched by `regexpr`

+ `expression ~ /regexpr/`     
Matches if the string value of `expression` contains a substring matched by `regexpr`

+ `expression !~ /regexpr/`     
Matches if the string value of `expression` does not contian a substring matched by `regexpr`

Here are some exmaples:

```bash
$4 ~ /Hello/  { ... }
$0 !~ /World/ { ... }
/Hell0/ { ... }
```

## Regular Expressions

The syntax of RE is similar with other language. 

The Parentheses are used in regular expressions to specify how components are groups. Here is an example:

```bash
(hello|Hello) (world|World) (good|bad)now

#Will match the string

hello world goodnow
Hello world badnow
```

In regular expressions, the alternation operator `|` has the lowest precedence. so the `ab|cd*e$` is the same as `(ab)|(cd*e$)`

We often **use the regular expression with the string-matching**. Next is some example to match the number.

```bash
/^[0-9]+$/  match any input line that consist of only digits
/^[+-]?[0-9]+[.]?[0-9]*$/  match a floating piint number with optional sign and optional exponent.
```

## Range pattern

A range pattern consists of two patterns separated by a comma, as in: `pat1, pat2`. For instance:

```bash
/Hello/, /World/ 
```

Match the line start with the first line contain Hello and end at the line contain the World, **if the World not found, it will matcht to the end of input.**

Another example:

```
FNR==1, FNR==5 { print FILENAME: ": " $0}
```

Match the line between the line 1 and 5, The built-in variable is `FILENAME` which represent the name of current input file.


## Actions

The pattern determines when the action is to be executed. There are many actions in awk. Now we will introduce that:

## Expression

Expression is very simple. The first of the expression we talk about is the: `constants and variables`

+ **Constants**

String constant is enclosing a sequence of characters in quotation marks
Numeric constant can like `1234` or `0.7E-1`

+ **Variable**

In awk, the type of variable is not declared, awk infer the type from context. **and uninitialized variable has the string value "" and the numeric value 0.**

+ **Built-In variable**

| variable |  Meaning |
| -------- | -------- |
| ARGC      | number of command-line arguments |
| ARGV      | array of command-line arguments |
| FILENAME  | name of current input file |
| FNR       | record number in current file |
| FS        | controls the input field separator |
| NF        | number of fields in current record |
| NR        | number of records read so far |
| OFMT      | outout format for number | 
| OFS       | output field separator  |
| ORS       | output record separator |
| RLENGTH   | length of string matched by match function |
| RS        | controls the input record separator |
| RSTART    | start of string matched by match function |
| SUBSEP    | subscript separator | 

+ **Field Variables**

The fields of the current input line are called `$1`, `$2`, through `$NF`; `$0` refers to the whole line. Fields can also be specified by expressions. And a field variable referring to a nonexistent field has as it initial value the null string, e.g. $(NF+1).

The number of fields can vary from line to line, but three is unsally an implementation limit of 100 fields per line.

+ **Arithmetic Operators** 

Awk provides the usual `+, -, *, /, % and ^` arithmetic operators. **All arithmetic is done in floating point.**

+ **Logical Operators**

THe logical operators `&&(AND)`, `||(OR)`, and `!(NOT)` are used to create logical expressions by combining other expressions. **Newlines may inserted after the `&&` and `||` operators.**

+ **Conditional expressions**

A conditional expression has the form:

```bash
expr1 ? expr2 : expr3
```
It just like the C conditional expression does.

+ **Assignement Operators**

There are seven assignment operators that can be used in expressions called assignments. They are `=, +=, -=, *=, /=, %= and ^=`.They meanings are similar: **v op= e has the same effect as v = v op= e, except that v is evaluated once.**

And awk also supports the `++`, `--`. They also do the same thing as C languange.

+ **Built-in Arithmetic functions**

| FUNCTION |  VALUE RETURNED |
| -------- | --------------- |
| atan2(y,x) | arctangent of y/x in the range -\\(\pi\\) to \\(\pi\\) |
| cos(x)  | cosine of x, with x in redians |
| exp(x)  | exponential function of x, \\(e^x\\) |
| int(x)  | integer part of x, truncated towards 0 when x > 0 |
| log(x)  | natural(base e) logarithm of x |
| rand()  | random number r, where `$0 \leqslant x < 1$` |
| sin(x)  | sine of x, with x in radians |
| sqrt(x) | square root of x | 
| srand(x) | x is new seed for rand() |

Useful constants cna be computed with these functions: `atan2(0,-1)` gives \\(\pi\\) and `exp(1)` gives `e`.

The function `rand()` reuturn a pseudo-random floating point number greater or equal to 0 and less than 1. **Calling srand(x) sets the starting point of the generator from x**. Calling srand() sets the starting point from the time of day.

The assignment:

```bash
 randint = int((n *rand() ) + 1)
```

+ **String Operators**

There is only one string operation , **concatenation**. It has no explict operator:

```bash
{ print NR ":" $0}
```

It will print the line with line number and a colon with no blanks.

+ **String as Regular Expressions**

In fact, any expression can be used as the right operand of these operators. Awk evaluates the expression, convert the value to a string if necessary,and interprets the string as a regular expression.

```bash
BEGIN { digits = "^[0-9]+$"}
$2 ~ digits

```
This code will print all lines in which the second field is a string of digits. And the expression can be concatenated, so a regular expression can be built up from components. 

```bash
BEGIN {
	sign = "[+-]?"
	decimal = "[0-9]+[.]?[0-9]*"
	fraction="[.][0-9]+"
	exponent = "([eE]" sign "[0-9]+)?"
	number = "^" sign "(" decimal "|" fraction ")" exponent "$"
}

$0 ~ number
```

In a matching exprssion, a quoted string like `"^[0-9]+$"` are equal to `/^[0-9]+$/`. **If the string in quotes is to match a literal occurrence of regular expression of a regular expression metacharacter, one extra backslash is needed to protecting backsalash itself. 

```bash
$0 ~ /(\+|-)[0-9]+/ 
$0 ~ "(\\+|-)[0-9]+"  #this two are equivalent

```

## Builtin String Functions

The function `index(s,t)` returns the leftmost position where the string `t` begin in `s`, or zero if `t` dose not occur in `s`.

```bash
index("banana","an") 
# returns  2
```

The function `match(s,r)` finds the left most longest substring in the string `s` that matched by the regular expression `r`, return index or 0. It also sets the built-in variables `RESART` to the index and `RLENGTH` to the length of the matched substring.

```bash
match($0, /[0-9]+[.]?[0-9]*/)
```

The function `split(s,a,fs)` split the string `s` into the array `a` according to the separator `fs` and return the number of elements.

The function `sprintf(format, expr1, expr2,...,exprn)` returns a string containing `expr1,expr2,...,exprn` formatted according to the `printf`.

The function `sub(r,s,t)` first finds the leftmost longest substring matched by the regular expression `r` in target string `t`. It then replaces the substring by the substitution string `s`. The `sub` function returns the number of substitutions made. The function `sub(r,s)` is synonym for `sub(r,s,$0)`.

```bash
sub(/(an)+/,"AN","banana") 
#return  bANa
sub(/(an)*/,"AN","banana")
#return ANbanana
```

The `leftmost longest` means that the leftmost match is found first.

```bash
sub(/(an)+/,"AN","banbana")
#return bANbana
```

The function `gsub(r,s,t)` is similar, except that it successively replaces the leftmost longest nonoverlapping substrings matched by `r` with `s` in `t`; it returns the number of substitutions made.

```bash
gsub(/an/, "AN","banana")
#return bANANa
```

In a substitution performed by either `sub(r,s,t)` or `gsub(r,s,t)`, any occurrence of the character `&` in `s` will be replaced by the substring matched by `r`

```bash
gsub(/a/,"&A&","banana")
#return baAanaAanaAa
```

The function `substr(s,p)` return the suffix of `s` that begins at position `p`. if `substr(s,p,n)` is used, only the first `n` characters of the suffix are returned. If the suffix is shorter than `n`, then the entire suffix is returned. 

```bash
$5 = substr("banana",2,3)
print $5
#return ana
```

Strings are concatenated merely by writing them one after another in an expression. we can use that to join row to line. 

```bash
{s = s substr($1,1,3) " " }
END {printt substr(s,length(s)-1)}
```

It will concatenate $1 of each line into one line. That may help you in sometimes.

> There are two idioms for coercing an expression of one type to the other
> number ""   concatenate a null string to number to coerce it to a string
> string + 0  add zero to  string to coerce it to a number


## Control-Flow Statements

All of these control-flow statemens were adopted from `C`. There are `if-else`, `for`,`while`,`do-while`.

+ **if-else**

```bash
if (expression)
	statement1
else if (expression)
	statement2
else
	statement
```

+ **while loop**

```bash
while (expression)
	statement
```

+ **for loop**

```bash
for(expression1; expression2;expression3)
	statement

#or 

for (variable in array)
    statment
```

+ **do while**

```bash
do
	statement
while (expression)
```

There are two statments for modifying how loops cycle: `break` and `continue`.

+ **next**   
The next statement causes awk to fetch the next input and begin matching patterns starting from the first pattern-action statement.

+ **exit**   
In an `END` action, the `exit` statement causes the program to terminate. In any other action, it causes the program to behave as if the end of the input had occurred; no more input and the `END` actions if any, are executed.

> A semicolon by itself denotes the empty statement.


## Arrays
Awk provides one-dimensional arrays for storing strings and numbers. Arrays and array elements need not be declared, nor is there any need to specify how many elements an array has.

For instance:

```bash
    { x[NR] = $0}
END { for(i = NR; i>0; i--) print x[i]}  # this store each line in x, and print them out

## associative arrays
{x["good"] += 1}
```

If we had written `x[good]` instead of `x["good"]`, the expression would have used the value of the variable `good` as the subscript, and since the variable is not initilized, the value would have been accumlated in `x[""]`

If we use the `for loop` with array, the order in which the subscript are considered is implenmentation dependent. Result are unpredicatable if new elements are added to the array by `statement`

You can determine whether a particular subscript occurs in an array with the expression:

```bash
subscript in A
```

The good practise to use this determine the element, we can in this way:

```bash
if("good" in x) ...

#do not use this

if(x["good"] != "")
```

The second way may has the side effect of creating `x["good"]`.And it neither is a test of whether the array `x` contains an element with value `good`

### delete statement

```bash
delete array[subscript]
```

### split function
 
The `split(str,arr,fs)`, split the string with field separator, if no third argument, FS is used. For instance

```bash
split("8/8/16",arr,"/")
#return arr and the value is  
arr["1"] = 8, arr["2"]= 8, arr["3"]= 16
```

Notice: 01 is not the same string as 1 and the string 10 comes before the string 2.
