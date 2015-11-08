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





## Links
+ [go interface basic](http://blog.csdn.net/zhangzhebjut/article/details/24974315)