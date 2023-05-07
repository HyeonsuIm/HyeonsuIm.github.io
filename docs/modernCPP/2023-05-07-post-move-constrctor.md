---
layout: default
parent: modernCPP
nav_order: 1
title: "모던 CPP"
last_modified_at: 2022-11-23T20:45:00+09:00
categories:
  - CPP
tags:
---

# move constructor

일반적으로 기존 객체를 신규 객체로 복사하는 경우, 복사 생성자가 호출하게 된다. 하지만 객체 내에 동작에 따라 동적 할당이 자주 발생하는 등의 오버헤드가 발생하게 된다. 일반적인 경우에는 문제가 되지 않지만, 임시 객체를 생성하여 사용하는 경우 등 이러한 오버헤드가 불필요한 경우가 있다. 이를 해결하기 위해 c++에서는 move를 제공하고 있다.

원리는 단순한데 move를 사용할 경우 기존 객체의 데이터를 신규 객체로 옮긴 후, 기존 객체의 데이터는 더이상 사용할 수 없게 처리하는 것이다. 
코드는 아래와 같이 작성할 수 있다.
```c++
class Test{
  public:
  Test()      // constructor
  {
    valCnt = 3;
    val = new int[valCnt];
  }

  ~Test()     // deconstructor
  {
    Release();
  }

  Test(const Test& rhs)// copy constructor
  {
    Release();
    valCnt = rhs.valCnt;
    val = new int[valCnt];
    memcpy(val, rhs.val, sizeof(int)*valCnt);
  } 

  Test(Test&& rhs)// move constructor
  {
    valCnt = rhs.valCnt;
    val = rhs.val;

    rhs.valCnt = 0;
    rhs.val == nullptr
  } 

  Test& operator=(const Test& rhs ) // copy assignment operator
  {
    Release();
    valCnt = rhs.valCnt;
    val = new int[valCnt];
    memcpy(val, rhs.val, sizeof(int)*valCnt);
    return *this;
  }
  Test& operator=(Test&& rhs) // move assignment operator
  {
    valCnt = rhs.valCnt;
    val = rhs.val;

    rhs.valCnt = 0;
    rhs.val == nullptr

    return *this;
  }

  void Release()
  {
    valCnt = 0;
    if(val)
    {
      delete[] val;
    }
  }
  int* val;
  int valCnt;
}
```

위 코드는 기본적인 move constructor 동작 코드이다. 복사 또는 operator로 전달되는 객체가 l-value인 경우 copy constructor, r-value인 경우 move constructor가 호출되게 된다.
l-value, r-value는 간단하게 아래와 같이 구분할 수 있다.

## l-value
표현식 이후에도 사라지지 않고 지속되는 객체

## r-value
표현식이 종료된 이후 없어지는 임시적인 값