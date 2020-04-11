---
title: 'Go:Writing Web Applications'
comments: true
typora-root-url: ../../source/
date: 2020-03-23 13:30:12
tags:
---

#### 可能的“陷阱”

正如前面所说，切片操作并不会复制底层的数组。整个数组将被保存在内存中，直到它不再被引用。 有时候可能会因为一个小的内存引用导致保存所有的数据。

例如， `FindDigits` 函数加载整个文件到内存，然后搜索第一个连续的数字，最后结果以切片方式返回。

```go
var digitRegexp = regexp.MustCompile("[0-9]+")

func FindDigits(filename string) []byte {
    b, _ := ioutil.ReadFile(filename)
    return digitRegexp.Find(b)
}
```

这段代码的行为和描述类似，返回的 `[]byte` 指向保存整个文件的数组。因为切片引用了原始的数组， 导致 GC 不能释放数组的空间；只用到少数几个字节却导致整个文件的内容都一直保存在内存里。

要修复整个问题，可以将感兴趣的数据复制到一个新的切片中