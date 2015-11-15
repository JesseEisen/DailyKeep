---
title: Awk Program
updated: 2015-11-14  22:30
---

### Output

With some string functions in awk, we can do some great things.

**1.split**

if we want to make up the date format we can use this trick:

	`date` | getline date
	split(date,d," ")
	date = d[1] " " d[2] "," d[6]   #here string date will like: Nov 14, 2015

Well, cammand date have some paramenters can output some format: date +%D  11/14/2015. 


**2.substr**

when we want to get a number's integer part and decimal part, we can use this function. first we can use the `sprintf` function to make the format unique. like `n = sprintf("%.2f",num)`

	deciaml = substr(n,length(n)-1,2)
	integer = substr(n,1,length(n)-3) 

Very easy, right?


**3.gsub**

Sometime, if we want to output a serial character, for example: 40 dashes.  In python, we can use `"-"*40`, but in awk, we can use this trick following:

	dashes = sprintf("%40s"," ")
	gsub(/ /, "-", dashes)

now the variable `dashes` will have 40 dashes. easy too? 


###Others

- [Keep growthing series in medium](https://medium.com/keep-learning-keep-growing/the-awkwardly-simple-solution-to-becoming-successful-986965ed5956)

Excerpt from this posts

> We waste loads of time trying to pre-qualify ourselves for opportunities we are already qualified for.


<embed src="http://music.163.com/style/swf/widget.swf?sid=125909811&type=0&auto=0&width=310&height=430" width="330" height="450"  allowNetworking="all">

