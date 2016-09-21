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

### condition

Sometimes, we want to use the `and or` to do the ternary operator thing. we should pay attention.
if we use this in our program:

```lua
> condition = true
>= condition and false or true
true
```

This condition will never act the right `or` expression. because the and has a higher precedence than `or`
so we can change to this:

```lua
> condition = true
>= not condition and true or false
false
```

## Table 

Table are created using `table constructors`, which are defined using curly brackets.

```lua
> t = {}
> print(t)
table:0035AE18
```

### using tables

To access the value associated with the key in a table you can use the `table[key] = value`

```lua
> t = {}
> t["foo"] = "bar"
> =t["foo"]
bar
> t["bar"]  --not an error, return nil instead
nil
```

**Any value can be used as a key, just make sure it's not `nil` or `NAN`

```lua
> t = {}
> k = {}
> f = function() end
> t[f] = 456
> =t[f]
456
```

There is a shortcut for use string as key, like:

```lua
> t = {}
> t.foo = 123
> =t.foo
123
> =t["foo"]
123
```
Another shortcut

```lua
> t = {foo = "bar"} -- not [foo]="bar", same as ["foo"]="bar"
> =t["foo"]
bar
```

To loop over all the key/value pairs in a table, use the `pairs` iterator

```lua
> t = {foo = "bar", [123] = 456}
> for key,value in pairs(t) do print(key,value) end
foo bar
123 456
```

> The order when looping with pairs is undefined. Just because you added one item after adding another doesn't mean that's the order they'll be in with pairs.
>Inside a pairs loop, it's safe to reassign existing keys or remove them (by assigning nil to them), but not to add new keys (that had a nil value previously). 

### Tables as arrays

There is no array type in lua, so we can treat table as array. see

```lua
> t = {'a', 'b', 'c'}
>=t[1]
a
```

We still can mix the array syntax with the usual key=value syntax:

```lua
> t = {"a", "b", [123]="foo", "c", name="bar", "d", "e"}
> for k,v in pairs(t) do print(k,v) end
1       a
2       b
3       c
4       d
5       e
123     foo
name    bar
```

To envalue the `length` of table, use `#`, 

```lua
> t = {1,2,3,4,5}
> #t
5
```

### Table values are reference

When you pass a table to a function or store it in a new variable, etc. a new copy of that table is not created. Tables do not act like numbers in these cases. Instead the variable or function becomes a reference to the original table. 
This is much like a pointer in the C Language. For example:

```lua
> t = {}
> u = t
> u.foo = "bar"
> = t.foo
bar
> function f(x) x[1] = 2 end
> f(t)
> = u[1]
2
```

A related thing to remember is that table comparison works by reference. Comparing tables using == will return false even if the two tables have the same contents. They must actually be references to the same table.

```lua
> t1= {1,2,3}
> u = t1
> t2 = {1,2,3}
> t1 == u
true
> t2 == t1
false
```

[copy a table manually](http://lua-users.org/wiki/CopyTable)


### Table as unordered sets

Referring this post for more detial. [sets and bags](https://www.lua.org/pil/11.5.html)

## Function 

Function are created with the `function` keyword as follows:

```lua
function (args) body end
```

### Function are values

The function block is an expression (in the same sense that "1 + 2" is an expression) that evaluates to a new function value. A function value can be called by using the ( ) operator, which runs the code in the function. The ( ) pair goes after the function expression, and optionally contains a comma-separated list of arguments.

One thing to remember is that like tables, functions are passed by reference. For example, when you assign a variable containing a function to another variable, you just create a new "handle" to the same function.

### Function arguments
Functions can take 0 or more arguments. These are values given to the function when it's called, that the code stored in the function can use. Inside the function, the parameters look like variables, except they only exist inside the function.

```lua
> g = function(value)
>>print(value)
>>end
> =g()
nil
> =g "example" -- the () can be omitted if you have one quoted string arg
example
> =g {}
table: 0x820ee0
```

# Function return values

Function can also return any amount of the values. 

```lua
> f = function()
>> return "x", "y", nil, "z"
>> end
> t = {f()}
> #t
4
> t[3]
nil
```

if the function returns nils, since nil in tables is considered "no value", , the # operator can't be reliably used to get the number of values because it's undefined if an array has "holes".

Now if you want to function discard return value, you can use the `()`.

```lua
> f = function()
>> return "x", "y", "z" 
>> end
> a,b = (f()) --wrapping a function call in () discards multiple return values
x, nil
>= "w"..f() -- using a function call as a sub-expression discards multiple returns
wx
> print("w", f())
w x y z
> print(f(), "w") -- same when used as the arg for another function call
x w
> print("w", (f())) 
w x 
```

If you're used to a language (like Python) that returns multiple values by storing them in a "tuple" type, that's not how Lua works. Lua functions actually return separate values, instead of a single container.

### Return skips other code

Use `return` to return from function, like the `exit` in c

### Function as parameters

A good example is `table.sort`, which can optionally take a custom less than function

```lua
> list = {{3},{5},{2},{-1}}
> table.sort(list, function(a,b) return a[1] < b[1] end)
> for i,v in ipairs(list) do print(v[1]) end
-1
2
3
5
```

### value number of arguments

A function can have `...` at the end of its argument list.Then you can use `...` inside the body of the function, and it will
envalue to the multiple values

```lua
> f = function(x, ...)
>> x(...)
>> end
> f(print, "1 2 3")
1 2 3
```

Using `select` function, which takes a number and a variable number of args, and **returns the args starting from that index**. It can also take "#" as the index and return the amount of args:

```lua
> f=function(...) print(select("#", ...)) print(select(3, ...)) end
> f(1,2,3,4,5)
5
3 4 5
```

`...` can be packed into a table, the most safe way to do that is:

```lua
> f=function(...) tbl=table.pack(...) print(tbl.n, table.unpack(tbl,1,tbl.n))end
> f("a",nil,"c")
3 a nil c
```

### named functions

function `function f(...)  end` equal to `f= function(...) end`

### Recursion and tail calls

The `mutually recursive` function is easy to understand, but when the level is too large, and will make the `call stack` full.so `tail recursive` is recommanded

```lua
function factorial_helper(i, acc)
  if i == 0 then
    return acc
  end
  return factorial_helper(i-1, acc*i)
end

function factorial(x)
  return factorial_helper(x, 1)
end
```

Not all non-tail called recursive functions are bad, it's often the most natural solution if the calls won't get very deep. But if you are expecting hundreds of iterations or more, you should probably consider using tail recursion or just a loop.


## Scope 

To create a local variable, add the `local` keyword before the assignment, you don't need the local keyword any more when changing the variable.

```lua
local a = 5
a = 6 --changes the local a, doesn't create a global
```

### local function syntax sugar

```lua
local function f() end

-- is equivalent to

local f
f = function() end

-- not

local f = function() end
```

the difference between the last two examples is important: the local variable still doesn't exist to the right of the = that gives it the initial value. So if the contents of the function used f to get a reference to itself, it will correctly get the local variable in the first and second versions, but the third version will get the global f (which will be nil, if not a completely unrelated value set by some other code).

