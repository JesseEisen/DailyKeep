---
title: Lua Syntax Overview
updated: 2016-08-08 19:00
---

## Assignment 
Setting the value of a variable is an Assignment.

```lua
> x = 1
> y = "hello"
```

### multiple Assignment

```lua
> x, y = 2, "there"
> print(x,y)
2, there
```

### Assignment order 

The order in which multiple assignments are performed is not defined. This means you shouldn't assume the assignments are made from left to right. If the same variable or table reference occurs twice in the assignment list the results may surprise you.

```lua
> a, a = 1, 2
> print(a)
1
```

## Numbers

Lua **only supports floating point numbers.** Default it is double precision floating point numbers.
The usage is same as c. 

### The math lirary
There are many math function provied by lua. like:`math.abs`, `math.sqrt`. More information refer the `MathLibrary Tutorial` below.

### Conversion


```lua
>= tonumber("123") + 25
148
> x = tonumber("123.456e5")
> print(x)
12345600
```

Lua  will automatically convert string and number types to correct format to perform calculations.

```lua
>= 100 + "7"
107
>= "hello" + 234
stdin:1: attempt to perform arithmetic on a string value
stack traceback:
        stdin:1: in main chunk
        [C]: ?
```

A notable exception: comparison operators (== ~= < > <= >=) do not coerce their arguments. The (in)equality operators consider a number to be not equal to its string representation (or any non-number type in fact). Ordered comparison operators throw an error when you feed them different types.

```lua
>= 100 == "100"
false
>= 100 ~= "hello"
true
>= 100 <= "100"
stdin:1: attempt to compare number with string
stack traceback:
        stdin:1: in main chunk
        [C]: ?
```

## Strings

The `quote and the escape sequences` are the same as c. Now, the different is the `multiple quotes`

### Multiline quotes

Double square brackets can be used to enclose literal strings which traverse serval lines.

```lua
>= [[multiple lines of text
>> can be enclose in double square
>> brackets.]]
Multiple lines of text
can be enclosed in double square
brackets.
```

### Nesting quotes

Double square brackets allow nesting, but they require one or more `=` inserted in the outer-most brackets to distinguish them. 
**It doesn't matter how many = are inserted, as long as the number is the same in the beginning and ending brackets.**

```lua
>= [=[ one [[two]] one]=]
one [[two]] one
>= [=[one [ [==[one]=]
one [ [==one 
```

### Concatenation

Lua use `..` to joined string together.

```lua
>= "hello " .. "world"
hello world
```

Number also can be Concatenation to strings. In this case they are coerced to string

```lua
>= "Green bottles:" ..10
Green bottles:10
```

Doing a large number of concatenation operations may be slow, so there are some faster ways to refer

```lua
-- slow
local s = ''
for i=1,10000 do s = s .. math.random() .. ',' end
io.stdout:write(s)

-- fast
for i=1,10000 do io.stdout:write(tostring(math.random()), ',') end

-- fast, but uses more memory
local t = {}
for i=1,10000 do t[i] = tostring(math.random()) end
io.stdout:write(table.concat(t,','), ',') 
```

Both two ways are directly to output the content.

There are many string library functions, refer the next post.

### Coercion

During coercion, we do not have full control over the farmatting of the conversion. so we can use  `string.format()`

```lua
>= string.format("%.3f",5.1)
5.100
```

## Operators 

The supported arthmetic operators: `+,-,*,/, -, ^, %`, and the relation expressions 

```
+ == equal to  
+ ~= not equal to   
+ < less than
+ > greater than
+ <= less than or equal to
+ >= greater than or equal to
```

### Logical operators

Lua support `and, or, not`. **In lua the `nil` and `false` represent false in a logical expression. 
Anything that is not false is `ture`, including 0**

```lua
-- although they are both considered false by logical operators, they're still different values
>= false==nil
false
>= true == false
false
```

The keyword `not` inverts a logical expression value:

```lua
>= true, false, not true, not false
true false false true
>= not nil
true
>= not "foo"  -- anything not false or nil is true
```

The keyword `and` may has some difference than other language. It not necessarily return the boolean value.
In lua, it will return the first argument if its value is `false` or `nil`, and the second value if the first argument is not `false` or `nil`.

```lua
>= false and true
false
>= nil and true
nil
> true and print("hello")
hello
```

The `or` binary operator also does not necessarily return a boolean value (see notes for and above). If the first argument is true it is returned, otherwise the second argument is returned. 
So, a boolean is only returned if the first argument is true or the second argument is a boolean.

```lua
>= true or false
true
>= nil or "hello"
hello
```

Sometimes, this can be a very useful property. For example. setting default values in a function:

```lua
> function foo(x)
>>   local value = x or "defalut"  -- if argument x is false or nil, value becomes "default"
>>   print(value,x)
>>end

> foo()
default nil
> foo(true)
true  true
```

### Ternary operators  三元

```lua
value = condition and trueval or falseval
```

## Control Structure

### if 

Here is the base condition:

```lua
if condition then
        block
elseif condition2 then
        block
else 
        block
end
```

### while and repeat

```lua
while condition do 
        block
end

repeat 
        block
until condition
```

### for 

+ numeric for

```lua
for variable = start, stop, step do
        block
end
```

Here is a example:

```lua
for i = 3, -3, -1 do
    print(i)
end
```

+ iterator for

```lua
for var1, var2,var3 in iterator do
   block
end
```

### break statement
do the same as C

### continue statement

Lua use the `goto` statement to implenment the continue, see:

```lua
for i =1, 10 do
   if i > 3 and i < 6 then goto continue end
   print(i)
   ::continue::
end
```

But Lua5.1 do not have the goto statement.