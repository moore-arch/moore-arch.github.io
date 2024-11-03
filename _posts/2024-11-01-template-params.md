---
author: moore
layout: post
title: c++ template
date: 2024-11-01 20:00 +0800
categories: [linux, cpp]
tags: cpp
mermaid: true
---

# c++模板
模板主要用于在编译期生成普通类型的函数或类，按照功能可以分为类模板和函数模板，`c++14`还引入的变量模板。
下面的例子中`template`表示下面的代码是一个模板，`typename`表示该参数是类型占位符（表示下一个变量名是类型未知标识符），可以用`class`代替。
```cpp
// T在编译器确定，根据不同的类型生成不同的函数
template <typename T>
bool Cmp(const T& left, const T& right) {
  return left < right;
}
```

## typename
`typename`可以在模板声明和定义的任意位置使用，但一般不允许在基类列表中使用（除非用于模板基类的模板参数）
例如下面的例子中，C1直接使用是不允许的，但是在C2中使用typename作为基类A的模板自变量（模板参数）则允许。
```cpp
// https://learn.microsoft.com/zh-cn/cpp/cpp/typename?view=msvc-170
template <class T>
class C1 : typename T::InnerType // Error - typename not allowed.
{};
template <class T>
class C2 : A<typename T::InnerType>  // typename OK.
{};
```

## c++模板参数
模板形参分为类型形参（type parameter）和非类型形参（non-type parameter）
- 类型形参：跟在关键字class或typename之后声明
- 非类型形参：跟在类型说明符之后声明。可以分为：整形或枚举类型，对象/函数指针，对象/函数引用，成员函数指针

### 类型参数
类型参数以`typename`/`class`关键字声明，类型参数可以有多个，直接使用`typename...`声明即可
```cpp
// 普通的类型参数
template <typename T, typename U, typename V> class Foo{};

// 数量可变的类型参数
template<typename... Arguments> class vtclass;
```

### 非类型参数
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

### 模板作为模板参数
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

### 默认模版自变量

类模板和函数模板的参数都可以指定默认自变量，类似于函数的默认参数，对于默认自变量使用时如果不指定值则使用默认值。多个模板参数，第一个默认参数后的所有参数必须具有默认参数。参数均为默认值的模板时，请使用空尖括号。
```cpp
template<typename A = int, typename B = double>
class Bar
{
    //...
};

int main()
{
    Bar<> bar; // use all default type arguments
}
```

## 参考内容
- https://wy-ei.gitee.io/notebook/posts/2020/chrono/#%E6%97%B6%E9%97%B4%E6%AE%B5
- https://subingwen.cn/cpp/chrono/index.html











