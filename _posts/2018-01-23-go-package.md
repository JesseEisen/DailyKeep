---
title: Go Package Example
updated: 2018-01-23 18:00
---

#### Scanner

> import "text/scanner"

用来扫描并标记一串UTF8的字符串。 基本使用如下：

```
var s scan.Scanner

//初始化一个scanner，并设置s.Mode为GoTokens
s.Init(strings.NewReader(string)) 
//同时设置SkipComments 和 ScanComments
s.Mode = scanner.ScanStrings | scanner.SkipComments | scanner.ScanInts | scanner.ScanCommets
tok = s.Scan() //从str里面读取下一个token

if tok != scanner.EOF {
	s.Next() //读下一个token
	s.TokenText() //返回最近一次读的token的string值
	scanner.TokenString(tok)  // 把token转换为可打印的
	scanner.TokenString(s.Peek()) //Peek中保存的是下一个token的内容
}

```