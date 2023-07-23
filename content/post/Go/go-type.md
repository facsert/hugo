---
title: Go 泛型
description: 
date: 2023-03-06 19:25:13
categories:
    - Go 教程
tags:
    - Go
---


## 类型约束

```go
func detail[T string| int| bool](item T) {       // 多类型参数定义
    fmt.Printf("value: %v \n", variable)         // 打印变量的值
    fmt.Printf("type: %T", variable)             // 打印变量的类型
}

func add[T string| int](a T, b T) {              // 多类型参数定义, a b 类型相同, 且都为 string 或 int
    fmt.Println(a + b)                           // 打印结果
}
```
