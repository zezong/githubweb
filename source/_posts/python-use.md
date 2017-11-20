---
title: python常用用法
date: 2017-11-20 09:43:27
tags: python
---

## python常用用法 

### 字符替换
python 字符串替换可以用2种方法实现:<br />
1、是用字符串本身的方法<br />
2、用正则来替换字符串<br />

例子： a = "hello word"
#### 1、用字符串本身的replace方法
``` bash
print a.replace('word', 'python')
```
输出结果是 hello python

#### 2、用正则来替换字符串
``` bash
import re
strinfo = re.compile('word')
print strinfo.sub('python', a)
```
输出结果是 hello python