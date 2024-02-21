---
author: moore
layout: post
title: c++ chrono
date: 2024-02-07 14:58 +0800
categories: [linux, cpp]
tags: cpp
mermaid: true
---

# chrono库

`c++11`开始提供了用于处理日期和时间的`chrono`库，`chrono`内主要包含了`duration`, `clocks`, `time_point`三个关键类型/概念。

## duration
`duration`用于表示一段时间间隔（时间段），记录时间长度，例如1s，10分钟等（一般采用1s作为最基本的时间单位）。一般定义为：
```cpp
// 特化，_Period是1，代表1s
template <class _Rep, class _Period = ratio<1> > class duration;

// 通用
template <class _Rep, class _Period>
class duration {}

// ratio分母默认为1
template<std::intmax_t Num, std::intmax_t Denom = 1>
class ratio {};
```
- `_Rep`：数值类型，一般为`int`，表示时钟周期`_Perion`的数量。
- `_Period`：表示时钟周期，实际上是一个分数`std::ratio`类型，`Num`表示分子，`Denom`表示分母。
`duration<_Rep, _Period>`表示`_Rep`个`_Period`。例如：

```cpp
std::chrono::duration<int, std::ratio<60, 1>> minute(1); // 1 min
std::chrono::duration<int, std::ratio<1, 1000>> ms(2); // 2ms
std::chrono::duration<int, std::ratio<1, 1>> second(12); // 12s

// 标准库定义
typedef duration<long long,milli> milliseconds;
typedef duration<long long> seconds;
typedef duration<long, ratio<60> > minutes;

typedef ratio<1LL, 1000LL> milli;
```
不同类型时间段（秒，分钟）之间可以直接转换，前提是不损失精度，如果损失精度则必须使用`duration_cast`完成：
```cpp
std::chrono::duration<int, std::ratio<1, 1>> n_second(22); // 22秒
std::chrono::duration<double, std::ratio<60, 1>> n_minute(n_second); // ok, 秒变分钟后由double表示

std::chrono::duration<int, std::ratio<60, 1>> n_minute(n_second); // 编译失败
chrono::minutes n_minute = chrono::duration_cast<chrono::minutes>(n_second); // ok，强制转换，损失精度
```
不同时间段之间可以进行加减、除操作，符合数学规律。

## time_point
`time_point`类型表示一个具体的时间点，一般定义为：
```cpp
template<class _Clock, class _Duration = typename _Clock::duration>
class time_point {};
```
- _Clock表示时钟类型，例如system_clock（1970 00:00计算，可修改系统时间）或steady_clock（自开机时间计算，稳定增长）
- _Duration表示自该时钟起始点经过的时间
显然，时间点可以通过增加/减少`duration`获取新的时间点
```cpp
std::chrono::system_clock::time_point now = std::chrono::system_clock::now();
auto next_day = now + std::chrono::hour(24); // 24h之后的时间点

// 获得时间点距离时钟起始时刻的 duration
decltype(now)::duration dur = now.time_since_epoch();
```

## clock

`chrono`中包含的时钟类型一般有3种：
- system_clock: 系统时钟，可以修改，自1970-1-1 00:00开始计算
- steady_clock: 固定时钟，不可修改，自开机计算
- high_resolution_clock: system_clock的别名

### system_clock
系统时钟，对应于系统时间，可以将当前时间与`time_t`类型相互转换。 
```cpp
// 返回表示当前时间的时间点。
static std::chrono::time_point<std::chrono::system_clock> now() noexcept;
// 将 time_point 时间点类型转换为 std::time_t 类型
static std::time_t to_time_t( const time_point& t ) noexcept;
// 将 std::time_t 类型转换为 time_point 时间点类型
static std::chrono::system_clock::time_point from_time_t( std::time_t t ) noexcept;
```

### steady_clock
`steady_clock`使用的是开启后的时钟，只要不重新启动就一定在增长，是稳定时钟。

## 转换

### duration_cast
`duration`之间的转换可以遵循规则相互转换，规则之外的借助`duration_cast`完成。
可以直接转换的情况：
- 时钟周期转换：源时钟周期可以整除目的时钟周期，例如从小时到分钟转换（不损失精度）
- 时钟周期次数转换：即对_Rep的转换，低精度像高精度转换，例如int到double
- 时钟周期和时钟周期次数都变化：依据时钟周期次数类型转换
除此之外，必须使用`duration_cast`完成转换。

### time_point_cast
`time_point_cast`是`chrono`中的模板函数，可以对时间点进行转换，不同时间点内的时钟周期`_Period`和周期次数`_Rep`可能不同，此时可以使用隐式转换或者显式转换。

```cpp
std::chrono::time_point<std::chrono::system_clock, std::chrono::seconds> tp_s(6);

std::chrono::time_point<std::chrono::system_clock, std::chrono::milliseconds> tp_ms(tp_s); // ok, 不损失精度

std::chrono::time_point<std::chrono::system_clock, std::chrono::seconds> ns(tp_ms); // error, 损失精度，不能直接隐式转换

tp_s = std::chrono::time_point_cast<std::chrono::seconds>(tp_ms); // ok, 显式转换，损失精度
```


# 参考内容
- https://wy-ei.gitee.io/notebook/posts/2020/chrono/#%E6%97%B6%E9%97%B4%E6%AE%B5
- https://subingwen.cn/cpp/chrono/index.html












