---
title: Python Documentation
updated: 2016-02-15  16:21
---

## List

> Syntax   
> [value, value, ...]   
> list([iterable])

Here are some methods for list:

+ `append(value)` -- appends a new element to the end of the list   
The value can be a single character, a list, a string or any other type.

+ `count(value)` -- count the times of ocurrences of some value in the list   
+ `extend(enumerable)` -- extends the list by appending elements from another enumerable   

```python
a = [1,2,3,4,5]
b = [6,7,8]

a.extend(b)
a.extend(range(3))
#or
a = a + b
```
+ `index(value,[startIndex])` -- get the index of the first occurrence of the input value. If a second argument is provided, the search is started at that specified index.  

+ `insert(index,value)` --  inserts value just before the specified index. Thus after the insertion the new element occupies position index.   
+ `pop([index])` -- removes and returns the item at index. With no argument it removes and returns the last element of the list.   
+ `remove(value)` -- removes the first occurrence of the specified value. If the provided value cannot be found, a ValueError is raised.  
+ `reverse()` -- reverses the list in-place and **returns None**
+ `sort()`  -- sorts the list **in numerical and lexicographical** order and return **None**

```python
a.sort(reverse=True)
a.sort(key=lambda item:item.name) #sort by items
```

+ `clear()` -- removes all items from the list  
+ Replication -- multiplying an existing list by an integer will produce a larger list consisting of that mayn copies of the original.

```python
b = ["blabla"] * 3
# b = ["blabla", "blabla", "blabla"]
```
Take care doing this if the list contains references to objects:

```python
li = [[]] * 3
#[[], [], []]

#and if we do use append 
li[0].append(1)
#[[1],[1],[1]]
```
This is because the `[[]] * 3` doesn't create a `list` of 3 different `list`.Rather it creates a `list` holding 3 references to the same `list` object. 

The solution is to create the inner lists with a loop

```python
li = [[] for _ in reange(3)]
#we can verified by use id function
[id(inner_list) for inner_list in li]
# [6331048, 6331528,6331488]
```

+ Element deletion -- it is possible to delete multiple elements in the list using the `del` keyword

```python
a = list(range(10))
del a[::2]
# a = [1,3,5,7,9]
del a[-1]
# a = [1,3,5,7]
del a[:]
# a = []
```

+ Copying  -- The default assignment `=` assigns a reference of the original list to the new name. **That is the original name and new name are both pointing to the same list object. 

```python
b = a
a.append(6)
```

if you want to create a copy of the list you have below options:

```python
nlist = old_list[:]
#or
nlist = list(old_list)
#or
import copy
nlist = copy.copy(old_list) 
#inserts references to the objects found in original list
```

If the list contains objects you want to copy them as well.

```python
import copy
nlist = copy.deepcopy(old_list)
#inserts copies of the objects found in the origin list
```





