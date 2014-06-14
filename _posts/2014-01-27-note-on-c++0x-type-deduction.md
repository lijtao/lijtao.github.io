---
layout: post
title: C++0x 学习之类型推导
categories: cs
tags: [c++,note]
---

本文是在学习 Bjarne Stroustrup 大神的[C++11 FAQ](http://www.stroustrup.com/C++11FAQ.html) 做的笔记，也参考了 **有{間}客栈** 的[翻译](http://www.chenlq.net/cpp11-faq-chs)，还有[中文wiki](http://zh.wikipedia.org/wiki/C++0x)。话不多说，切入正题。

<!-- more -->

### auto：由初始化推导类型
auto 可根据变量的初始化语句自动推导类型。

c++0x里可以这样写：

```c++
template<class T> void printall(const vector<T>& v)
{
    for (auto p = v.begin(); p!=v.end(); ++p)
        cout << *p << "\n";
}
```
    
而以前的 C++98 需要这样写：

```c++
template<class T> void printall(const vector<T>& v)
{
    for (typename vector<T>::const_iterator p = v.begin(); p != v.end(); ++p)
        cout << *p << "\n";
}
```

当一个变量的数据类型依赖于模板参数时，如果不使用auto关键字，将很难确定变量的数据类型，现在用 `auto` 可以轻松搞定了。例如：

```c++
template<class T,classs U> void multiply(const vector<T>& vt, const vector<U>& vu)
{
	// …
	auto tmp = vt[i]*vu[i];
	// …
}
```

### decltype：推断表达式的数据类型
如果需要根据初始化语句推导变量类型，用 `auto` 已经可以了；除非你需要得到某个非变量的类型，如函数返回值，你才需要使用 decltype。

```c++
void f(const vector<int>& a, vector<float>& b)
{
	typedef decltype(a[0]*b[0]) Tmp;
	for (int i=0; i<b.size(); ++i) {
		Tmp* p = new Tmp(a[i]*b[i]);
		// ...
	}
	// ...
}
```
### 返回值类型后置语法
看下面的代码：

```c++
	template<class T, class U>
	auto mul(T x, U y) -> decltype(x*y)
	{
		return x*y;
	}
```

我们不能这样写，因为x和y不在作用域内：

```c++
template<class T, class U>
decltype(x*y) mul(T x, U y) // 注意这里的作用域
{
	return x*y;
}
```

当然你可以这样写，如果你受得了的话，呵呵：

```c++
template<class T, class U>
decltype(*(T*)(0)**(U*)(0)) mul(T x, U y)    
{
	return x*y;
}
```

返回值后置语法最初并不是用于模板和返回值类型推导的，它实际是用于解决作用域问题

```c++
struct List {
	struct Link { /* ... */ };
	Link* erase(Link* p);   // 移除p并返回p之前的链接
	...
};
// 第一个List::是必需的，这仅是因为List的作用域直到第二个List::才有效。
List::Link* List::erase(Link* p) { /* ... */ }
// 更好的表示方式： 
auto List::erase(Link* p) -> Link* { /* ... */ }
```
