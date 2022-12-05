---
title: chrono时钟库
date: 2022-12-05 16:19:49
tags: C++
---

#### `std::chrono`时钟库

`chrono`是一个模版库，提供关于时间和日期的一些功能，主要包含三个模版。

* `clock`：时钟
* `time_point`：时间点
* `duration`：持续时间

#### `clocks`

时钟主要有三个，包括：`system_clock`，`steady_clock`，`high_resolution_clock`，大多情况下使用`system_clock`：

`1.system_clock`：系统时间，一般是`unix`时间，即从1970年1月1日到现在的时间间隔。

`2.steady_clock`：单调时间，即每后一次调用都会比前一次调用的时间要晚，并不反应真实时间的时间。通过该时钟可以获取一段时间的的间隔，不随系统时间而改变。

`3.high_resolution_clock`：高精度时间，用来测量细微间隔的时间。

##### `system_clock`

`1.now`：获取当前时间

`2.to_time_t`：用来将系统时间转变为`std::time_t`类型

`3.from_time_t`：用来将`std::time_t`转变为系统时间

```c
#include <iostream>
#include <chrono>
#include <iomanip>

int main()

{   

    //获取当前时间*

    auto time = std::chrono::system_clock::now();

    //装换为time_t类型，即为unix时间到现在的秒数*

    auto tt = std::chrono::system_clock::to_time_t(time);

    //localtime将time_t转换为本地时间格式的tm，put_time将时间转换为字符串格式化输出*

    std::cout << std::put_time(std::localtime(&tt), "%Y-%m-%d %H:%M:%S") << std::endl;

    return 0;

}
```

`steady_clock`用法

```c
	int i = 0;
	//获取时间
    auto begin = std::chrono::steady_clock::now();
    while (i++ < 1000000000);
    auto end = std::chrono::steady_clock::now();

    //时间段
    std::chrono::duration<double> time = end - begin;
    std::cout << "time = " << time.count();
```

#### `duration`持续时间

持续时间表示时间段，比如`1s`、`1min`等，定义如下：

```c
// Rep表示单位的数量单位
// Period表示单位
template<class Rep,  class Period = std::ratio<**1**>> class duration;
```

`period`表示时间的单位，比如天，秒等，`Rep`表示容纳某个数量时间的单位，即存储类型，如`int`，`double`等

```c
// Num，表示分子
// Denom，表示分母
template<std::intmax_t Num, std::intmax_t Denom = 1> class ratio;
```

举个简单点的例子，`Num`为1，`Denom`也为1时，表示是`1s`，如果`Num`为1，`Denom`为1000，那么相当于是1/`1000s`，也即毫秒。如果`Num`为60，`Denom`为1，那么表示 60 / `1s`，也即相当于`1min`。

```c
	std::chrono::milliseconds mills(3);
    std::cout << mills.count() << std::endl;
    //类型强制转换可以用duration_cast
    auto s = std::chrono::duration_cast<std::chrono::duration<double>>(mills);
    std::cout << mills.count() << std::endl;
    std::chrono::seconds secs(10);
    std::cout << secs.count() << std::endl;
    //duration重载了=/-运算符
    std::cout << (secs + mills).count() << std::endl;
    std::cout << (secs + mills + s).count() << std::endl;
```

```c
3
3
10
10003
10006
```

##### `time_point`时间点

定义如下

```c
//clock,时钟
//Duration,时间段
template<class Clock, class Duration = typename Clock::duration> class time_point; 
```

主要方法

* `time_since_epoch`，获取以模板时钟的起始时间到现在的这个持续时间。
* `time_point_cast`，用来将时间点转换为基于同一个时钟，但不同类型持续时间的时间点。
* 时间点也重载了`+/-`运算符，用来进行两个时间点之间的相加减。

注意：分清楚`Clock`时钟，`duration`持续时间，`time_point`三个的关系这部分简单使用挺容易的。
