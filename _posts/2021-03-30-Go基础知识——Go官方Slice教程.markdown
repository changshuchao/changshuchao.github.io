---
layout: article
title: "Go基础知识——Go官方Slice教程"
tags: Go基础知识
aside:
  toc: true
sidebar:
  nav: go-common-release
header:
  theme: dark
  background: 'linear-gradient(135deg, rgb(34, 139, 87), rgb(139, 34, 139))'
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
    src: /docs/assets/images/cover3.jpg
---



参考链接：

https://github.com/golang/go/wiki/SliceTricks

https://jishuin.proginn.com/p/763bfbd39306



测试代码：

```go
var (
    six  = []int{0, 1, 2, 3, 4, 5}
    five = []int{6, 7, 8, 9, 10}
)

func testAppend(a, b []int) {
    a = append(a, b...)
    fmt.Println(len(a))
    fmt.Println(cap(a))
}

func testCopy(src []int) {
    dest := make([]int, len(src))
    copy(dest, src)
    fmt.Println(len(dest))
    fmt.Println(cap(dest))
}

func testCopy2(src []int) {
    dest := append([]int{}, src...)
    copy(dest, src)
    fmt.Println(len(dest))
    fmt.Println(cap(dest))
}

func removeIndex1(a []int, index int) {
    a = append(a[:index], a[index+1:]...)
    fmt.Println(a)
    fmt.Println(len(a))
    fmt.Println(cap(a))
}

func removeIndex2(a []int, index int) {
    a = a[:index+copy(a[index:], a[index+1:])]
    fmt.Println(a)
    fmt.Println(len(a))
    fmt.Println(cap(a))
}

func cutRange(a []int, left, right int) {
    a = append(a[:left], a[right:]...)
    fmt.Println(a)
    fmt.Println(len(a))
    fmt.Println(cap(a))
}

func cutAndReleaseObject(a []int, left, right int) {
    copy(a[left:], a[right:])
    //for n := len(a) - right + left; n < len(a); n++ {
    //    a[n] = nil
    //}
    a = a[:len(a)-right+left]
    fmt.Println(a)
    fmt.Println(len(a))
    fmt.Println(cap(a))
}

func insertInto(origin, in []int, index int) {
    origin = append(origin[:index], append(in, origin[index:]...)...)
    fmt.Println(origin)
    fmt.Println(len(origin))
    fmt.Println(cap(origin))
}

func pop(in []int) (data int) {
    in, data = in[:len(in)-1], in[len(in)-1]
    return
}

func push(in []int, data int) {
    in = append(in, data)
}

func shift(in []int) (data int) {
    data, in = in[0], in[1:]
    return
}

func unshift(in []int, data int) {
    in = append([]int{data}, in...)

}
```

