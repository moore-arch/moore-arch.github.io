---
author: moore
layout: post
title: c++ template params
date: 2024-11-01 20:00 +0800
categories: [linux, cpp]
tags: cpp
mermaid: true
---

# c++模板参数
模板形参分为类型形参（type parameter）和非类型形参（non-type parameter）
- 类型形参：跟在关键字class或typename之后声明
- 非类型形参：跟在类型说明符之后声明。可以分为：整形或枚举类型，对象/函数指针，对象/函数引用，成员函数指针

## 类型参数
类型参数以`typename`/`class`关键字声明，类型参数可以有多个，直接使用`typename...`声明即可
```cpp
// 普通的类型参数
template <typename T, typename U, typename V> class Foo{};

// 数量可变的类型参数
template<typename... Arguments> class vtclass;
```

## 非类型参数
c++支持非类型参数，即值参数。其中N对应的类型就是值参数，调用的时候直接使用`UserArray<int, 10> lst`即可，其中N必须是`const`/`constexpr`。
```cpp
template <typename T, typename size_t N>
class UserArray {
public:
  UserArray() = default;
private:
  T data[N];
}
```

## 模板作为模板参数
模板同样可以作为模板参数，下面的例子中T是类型参数，Arr是模板参数
```cpp
// 更简洁的写法是省略Arr的类型参数名：
// template <typename T, template<typename, int> class Arr>
template <typename T, template<typename U, int N> class Arr>
class {
  T t; //OK
  Arr<T, 10> a;
  U u; //Error. U not in scope
}

```

# 参考内容
- https://wy-ei.gitee.io/notebook/posts/2020/chrono/#%E6%97%B6%E9%97%B4%E6%AE%B5
- https://subingwen.cn/cpp/chrono/index.html











