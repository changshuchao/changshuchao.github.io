---
layout: article
title: "Go基础知识——byte、rune和字符串"
tags: Go基础知识
aside:
  toc: true
sidebar:
  nav: go-common-release
permalink: /go-common/字符串
header:
  theme: forest
---


参考：http://golang.iswbm.com/en/latest/c01/c01_04.html



## byte&rune 

### 1. byte

#### 1.1 说明

byte，占用1个节字，就 8 个比特位（2^8 = 256，因此 byte 的表示范围 0->255），所以它和 **`uint8` 类型本质上没有区别**，它表示的是 ACSII 表中的一个字符。

#### 1.2 示例

```go
func testByte() {
    var a byte = 65
    //var b byte = '\101' //8进制写法:其中 \ 是固定前缀
    //var c byte = '\x41' //16进制写法: 其中 \x 是固定前缀

    var d uint8 = 66

    fmt.Printf("a : %c | d : %c \n", a, d)
    fmt.Println("a : ", string(a), " | d : ", string(d))

}

输出：
a : A | d : B 
a :  A  | d :  B

```

在 ASCII 表中，由于字母 A 的ASCII 的编号为 65 ，字母 B 的ASCII 编号为 66，所以上面的代码也可以写成这样:

```go
import "fmt"

func main() {
    var a byte = 'A'
    var b uint8 = 'B'
    fmt.Printf("a 的值: %c \nb 的值: %c", a, b)
    
}

```



### 2. rune

#### 2.1 说明

rune，占用4个字节，共32位比特位，所以**它和 `uint32`本质上也没有区别**。它表示的是一个 Unicode字符（Unicode是一个可以表示世界范围内的绝大部分字符的编码规范）。

#### 2.2 示例

```go
func testRune() {
    var a byte = 'a'
    var b rune = 'b'

    fmt.Printf("sizeof a : %d \n sizeof b : %d ", unsafe.Sizeof(a), unsafe.Sizeof(b))   //以10进制输出占的字节数
}

输出：
sizeof a : 1 
sizeof b : 4

```



### 3. 应用说明

由于 byte 类型能表示的值是有限，只有 2^8=256 个。**所以如果你想表示中文的话，你只能使用 rune 类型**。

```go
func testRune() {

    //var name byte = '中'   //报错：constant 20013 overflows byte
    var name1 rune = '中'
    fmt.Printf("name1 : %c \n", name1)
}

输出：
name1 : 中

```



### 4. 注意事项 

- 在 Go 中单引号与 双引号并不是等价的。单引号用来表示字符，在上面的例子里，如果你使用双引号，就意味着你要定义一个字符串，赋值时与前面声明的前面会不一致，这样在编译的时候就会出错。
- 既然byte 和 uint8 没有区别，rune 和 uint32 没有区别，那为什么还要多出一个 byte 和 rune 类型呢？理由很简单，因为uint8 和 uint32 ，直观上让人以为这是一个数值，但是实际上，它也可以表示一个字符，所以为了消除这种直观错觉，就诞生了 byte 和 rune 这两个别名类型。





## 字符串

### 1. 说明 

byte 和 rune 都是字符类型，若多个字符放在一起，就组成了字符串，也就是这里要说的 string 类型。

```go
func testString() {
    var mystr01 string = "hello"
    var mystr02 [5]byte = [5]byte{104, 101, 108, 108, 111}
    fmt.Printf("mystr01: %s\n", mystr01)
    fmt.Printf("mystr02: %s", mystr02)
}

输出：
mystr01: hello
mystr02: hello

```

**=》string 的本质，其实是一个 byte数组**



### 2. 字节数举例

`"hello,中国"` 占用几个字节？

要回答这个问题，你得知道 Go 语言的 string 是用 uft-8 进行编码的，英文字母占用一个字节，而中文字母占用 3个字节，所以`hello,中国` 的长度为 5+1+（3＊2)= 12个字节。

```go
func sizeOfString(){
    var country string = "hello,中国"
    fmt.Println(len(country))
}

输出：
12

```



### 3. 其他表示方式（转义情况） 

除了双引号之外 ，你还可以使用反引号。大多情况下，二者并没有区别，**但如果你的字符串中有转义字符`\`，这里就要注意了，它们是有区别的。使用反引号号包裹的字符串，会忽略里面的转义**。



#### 3.1 两种表示法 

比如我想表示 `\r\n` 这个 字符串，使用双引号是这样写的，这种叫解释型表示法

```go
var mystr01 string = "\\r\\n"
```

而使用反引号，就方便多了，所见即所得，这种叫原生型表示法

```go
var mystr02 string = `\r\n`
```



#### 3.2 示例

```go
func testStringEscape() {
    var a string = "\nhello"
    //var a string = "\hello"  //不能这么写，会报错：Invalid escape sequence
    var b string = "\\hello"
    var c string = `\\hello`
    fmt.Println(a)
    fmt.Println(b)
    fmt.Println(c)
}

输出：
hello
\hello
\\hello

```



#### 3.3 fmt标准输出转义好的结果 

如果你仍然想使用解释型的字符串，但是各种转义实在太麻烦了。你可以使用 fmt 的 `%q` 来还原一下。

```go
func testStringEscape() {
    var d string = `\\hello\n`
    fmt.Printf("解释型字符串是: %q",d)
}

输出：
解释型字符串是: "\\\\hello\\n"

```



#### 3.4 反引号换行

反引号可以不写换行符（因为没法写）来表示一个多行的字符串。

```go
func testStringEscape() {
    var e string = `i
love
you`
    fmt.Println(e)
}

输出：
i
love
you

```
