---
layout: default
parent: modernCPP
nav_order: 1
title: "type deduction"
last_modified_at: 2023-06-27T23:00:00+09:00
categories:
  - CPP
  - modernCPP
tags:
---

# type deduction
컴파일 단계에서 type을 결정하는 것을 말한다.
template와 auto 등의 경우 컴파일 단계에서 type이 결정된다.

<br><br>

# Template에서의 type deduction

## 참조 또는 포인터 형식의 type deduction
참조 또는 포인터 형식의 경우 const type이 그대로 사용되고 type에 맞게 그대로 전달되게 된다.

* 참조형식
  ```C++
    template<typename T>
    void f(T& param);

    int x=27;
    const int cx = x;
    const int& rx = x;

    f(x);   // T의 타입은 int       , param은 int&
    f(cx);  // T의 타입은 const int , param은 const int&
    f(rx);  // T의 타입은 const int , param은 const int&
    ```
* const 참조형식
    ```C++
    template<typename T>
    void f(const T& param);

    int x=27;
    const int cx = x;
    const int& rx = x;

    f(x);   // T의 타입은 int       , param은 const int&
    f(cx);  // T의 타입은 int       , param은 const int&
    f(rx);  // T의 타입은 int       , param은 const int&
    ```

* 포인터 형식
    ```C++
    template<typename T>
    void f(T* param);

    int x = 27;
    const int *px = &x;

    f(&x);  // T의 타입은 int       , param은 int*
    f(px);  // T의 타입은 const int , param은 const int*

<br>



## universal reference 형식의 type deduction
우선 universal reference란 l-value와 r-value를 가질 수 있는 참조 형식을 의미하며, 이 경우 상황에 따라 타입이 결정된다.
* type이 r-value인 경우 : r-value 파라미터로 타입이 결정 됨.
* type이 l-value인 경우 : l-value 참조 형식으로 타입이 결정 됨.

```C++
template<typename T>
void f(T&& param);

int x = 27;
const int cx = x;
const int& rx = x;

f(x);   // T의 타입은 int&        , param은 int&
f(cx);  // T의 타입은 const int&  , param은 const int&
f(rx);  // T의 타입은 const int&  , param은 int&
f(27);  // T의 타입은 int         , param은 int&&
```

## 값 전달 형식의 type deduction
값 전달 방식은 전달하는 type이 그대로 사용되며, 값 전달이기 때문에 const는 type에서 제외된다.

```C++
template<typename T>
void f(T param);

int x = 27;
const int cx = x;
const int& rx = x;

f(x);   // T의 타입은 int         , param은 int
f(cx);  // T의 타입은 int         , param은 int
f(rx);  // T의 타입은 int         , param은 int
```

```C++
template<typename T>
void f(T param);

const char* const ptr = "example"
f(ptr)  // const char * const 형식의 인수로 포인터 값 전달
```

## 배열과 함수의 type deduction

값 전달 방식을 사용하여 배열을 전달했을 때, 포인터 형식의 값이 전달된다
참조 전달 받식을 사용할 경우 고정사이즈 배열이 전달된다.
```C++
const char name[] = "abcde"

template <typename T>
void f1(T param);
template <typename T>
void f2(T& param);

f1(name);  // T의 타입은 const char *
f2(name);  // T의 타입은 const char[13]

// 이와 같은 동작을 이용해 배열 사이즈를 구할 수도 있다.
template<typename T, std::size_t N>
constexpr std::size_t arraySize(T (&)[N]) noexcept
{
  return N;
}
```

함수 전달 방식도 유사하게 동작한다.
```C++
void someFunc(int, double);

template<typename T>
void f1(T param);
template<typename T>
void f2(T& param);

f1(someFunc); // T의 타입은 void(*)(int, double)
f2(someFunc); // T의 타입은 void(&)(int, double)
```

<br><br>

# auto에서의 type deduction
auto는 기본적으로 template과 동일하게 동작하며, T와 auto부분이 동일하게 동작한다.
즉, auto부분에 T를 대입해보면 동작을 알 수 있다.
```C++
// int type
template<typename T>
void func1(T param);
func1(27)
auto x = 27;

// int type
template<typename T>
void func2(const T param);
func2(x);
const auto cx = x;

// int& type
template<typename T>
void func3(const T& param);
func3(x);
const auto& rx = x;
```

auto와 template이 동일하게 동작하지 않는 유일한 예외는 중괄호를 이용한 초기화를 할 경우이다.<br>
이에 대해 다르게 처리되는 이유는 따로 확인된 바가 없는 듯 하다.
```C++
auto x = {1,2,3}; // std::initializer_list<int>
template<typename T>
void func(T param);
fimc({1,2,3})     // compile error
```

# decltype
decltype은 표현식의 형식을 알려주며, template이나 auto와는 다르게 해당하는 타입이 그대로 반환되게 된다.
```C++
vector<int> v;    // decltype(v)    : vector<int>
v[0]              // decltype(v[0]) : int&
```

일반적으로는 아래와 같이 파라미터 값에 따라 반환값이 변경되거나, auto의 type정의 방식과 다르게 정의할 때 사용하게 된다.
```C++
template<typename Container, typename Index>
auto authAndAcess1(Container& c, Index i) -> decltype(c[i])
{
  return c[i];
}

// 또는
template<typename Container, typename Index>
deltype(auto) authAndAcess2(Container& c, Index i)
{
  return c[i];
}

const int& value = 1;
deltype(auto) value1 = value;   // const int &
```