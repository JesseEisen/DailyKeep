---
title: Go Type Assertions
updated: 2015-11-08  20:21
---

## Go Part

### Go type assertions

**1.First way**
We can use this at switch case. In this case, we can use `type` to make a switch. Like:

	switch t.(type) {
		case Int: 
			do something
		case String:
			do something
	}

Here is a way to use type assertion

**2.Second way**
In go language specification, we can see:

> More precisely, if T is not an interface type, x.(T) asserts that the dynamic type of x is identical to the type T. In this case, T must implement the (interface) type of x; otherwise the type assertion is invalid since it is not possible for x to store a value of type T. If T is an interface type, x.(T) asserts that the dynamic type of x implements the interface T.

> If the type assertion holds, the value of the expression is the value stored in x and its type is T. If the type assertion is false, a run-time panic occurs. In other words, even though the dynamic type of x is known only at run time, the type of x.(T) is known to be T in a correct program.

For example:

	example 1:

		var i interface{} = "I"

		_,ok := i.(string)  //ok will be true
		if ok {
			...
		}else{
			...
		}

	example 2:
		
		type struct1  struct {
			name   string
		}

		type struct2 struct {
			age   int
		}

		type I interface{
			sayhi()
		}

		func (s struct1) sayhi(){
			fmt.Println("hi",s.name)
		}

		func main(){
			var s1 struct1
			var s2 struct2
			var i1  I = s1
			var i2  I = s2

			// here will print ok
			_,ok := i1.(struct1)
			if ok{
				fmt.Println("ok")
			}else{
				fmt.Println("bad")
			}
			
			// here will go wrong, because struct2 does not implement I
			//(missing sayhi method)
			_, yep := i2.(struct2)   
			if yep{
				fmt.Println("yep")
			}else{
				fmt.Println("nope")
			}

		}


## JavaScript Part

### 可选的分号

有时候分号是可以省略不写的，这取决于下一条语句是什么。

	- 如果此时第二条语句开头是"(" "[" "/" "+" "-" 的时候一般上一条语句的分号不可以不写
	- 在return break continue和随后的表达式之间不能有换行
	- "++" "--" 这两个一般会作为下一行代码的前缀，如果不加分号的话

### 运算符 

+ Math.pow(2,10) //1024
+ Math.pow(3,1/3) //3的立方根

+ 当算术运算溢出时，如果超过所能表示的数的上限的时候，返回`Infinity`,负数的值超过所能表示的范围时候返回`-Infinity`
+ 下溢表示无限接近于零的时候， 返回`0`， 如果是负数的话，会返回`-0` 这个和0没有太多的差别。
+ 整除0 并不是报错，而是返回一个`Infinity`或者是`-Infinity`. 0/0 没有意义，会返回`NAN`

### 二进制浮点数和四舍五入错误
在JavaScript中，使用的是IEEE-754浮点数表示法，而在JavaScript中，这种表示对于分数 而言只是一种近似的表示。不能精确的表示0.1这样的数字。

	var x = .3 - .2;
	var y = .2 - .1;
	x == y   //false
	x == .1  //false,the result is 0.099 999 999 999 999 98
	y == .1  //true




## Links
+ [go interface basic](http://blog.csdn.net/zhangzhebjut/article/details/24974315)