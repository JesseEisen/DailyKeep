---
title: Latex fast note
updated: 2017-04-13 18:00
---

### 安装

```
sudo apt-get install texlive-full latex-beamer
```

### 中文字体使用

```
\documentclass{article}  
\usepackage{CJKutf8}  
\begin{document}  
\begin{CJK}{UTF8}{gkai}  
这是一个楷体中文测试，处理简体字。  
\end{CJK}  
\begin{CJK}{UTF8}{gbsn}  
这是一个宋体中文测试，处理简体字。  
\end{CJK}  
\begin{CJK}{UTF8}{bkai}  
這是一個big5編碼的楷體中文測試，處理繁體文字。  
\end{CJK}  
\begin{CJK}{UTF8}{bsmi}  
這是一個个big5編碼的明體中文測試，處理繁體文字。  
\end{CJK}  
\end{document}  
```
使用pdflatex编译。

### 使用系统字体

使用思源衬线体，xelatex编译

```
\usepackage{fontspec}
\defaultfontfeatures{Mapping=tex-text,Scale=MatchLowercase}
\setmainfont{Source Han Serif SC}
```

### 段落和行设置

```
\usepackage{indentfirst} %首个段落缩进
\setlength\parindent{0.5cm} % 缩进的宽度
\linespread{1.4}  % 行高
\setlength\parskip{0.5\baselineskip} %段落间的距离

\usepackage{seqsplit} %长句子无法自动断句的情况下

```

### 代码段

```
% 宏包
\usepackage{listings}
\usepackage{color}

%颜色设置
\definecolor{dkgreen}{rgb}{0,0.6,0}
\definecolor{gray}{rgb}{0.5,0.5,0.5}
\definecolor{darkgray}{rgb}{0.25,0.25,0.25}
\definecolor{mauve}{rgb}{0.58,0,0.82}

%listing的基本设置
\lstset{frame=tb,
language=C,
aboveskip=3mm,
belowskip=3mm,
showstringspaces=false,
columns=flexible,
basicstyle={\small\ttfamily},
numbers=none,
numberstyle=\tiny\color{gray},
keywordstyle=\color{blue},
commentstyle=\color{dkgreen},
stringstyle=\color{mauve},
breakatwhitespace=true,
breakatwhitespace=true,
tabsize=3,
%backgroundcolor=\color{darkgray}
}
```

使用代码段

```
\begin{lstlisting}
....
\end{lstlisting}
```

### 超链接

```
\usepackage[hidelinks]{hyperref} % 去掉丑的框
% 设置链接的样式
\hypersetup{colorlinks = true,
	urlcolor   = blue,
	linkcolor  = blue,
	citecolor  = red
}
```

### 插入jpg图片

```
\usepackage{graphicx}

%使用
\begin{figure}[ht!]
\centering
\includegraphics[width=90mm]{xxx.jpg}
\caption{图片标题}
\end{figure}
```

在标题处还可以设置一下label

```
\caption{标题是\label{overflow}}

%引用这个图片
And here we see figure \ref{overflow}.
```


