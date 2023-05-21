---
layout: default
parent: modernCPP
nav_order: 1
title: "modernCPP"
last_modified_at: 2022-11-23T20:45:00+09:00
categories:
  - CPP
tags:
---

# move constructor

일반적으로 기존 객체를 신규 객체로 복사하는 경우, 복사 생성자가 호출하게 된다. 하지만 객체 내에 동작에 따라 동적 할당이 자주 발생하는 등의 오버헤드가 발생하게 된다. 일반적인 경우에는 문제가 되지 않지만, 임시 객체를 생성하여 사용하는 경우 이러한 오버헤드가 불필요한 경우가 있다. 이를 해결하기 위해 c++에서는 move를 제공하고 있다.

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

# ptr
## shared ptr
데이터를 자동 할당해제해 주는 용도로 사용.
데이터와 reference 수를 같이 보관하여 참조하는 데이터 개수를 기반으로 동작하는 ptr 데이터 개수가 0으로 감소될 때 메모리가 해제 됨.
```c++
#include <iostream>
#include <memory>

using namespace std;

int main()
{
    shared_ptr<int> ptr3;
    {
        shared_ptr<int> ptr;
        ptr.reset(new int);

        cout << ptr.use_count() << endl;    // 1

        shared_ptr<int> ptr2(ptr);
        cout << ptr.use_count() << endl;    // 2
        cout << ptr2.use_count() << endl;   // 2
        ptr3=ptr;
        cout << ptr3.use_count() << endl;   // 3
    }

    cout << ptr3.use_count() << endl; //1
}
```
    1
    2
    2
    3
    1


## weak_ptr
shared_ptr사용 시 reference count에 관여하지 않는 ptr이다. 메모리 할당/해제에 관여하지 않고, 할당되어 있는 경우에만 사용할 때 사용한다.
shared_ptr 사용 시 서로 순환 참조를 하는 상황인 cyclic reference 문제 해결을 위한 용도로 한쪽에서는 weak_ptr을 사용하기도 한다.
```c++
#include <iostream>
#include <memory>

using namespace std;

int main()
{
    shared_ptr<int> ptr;
    weak_ptr<int> weak_ptr(ptr);
    *ptr = 5;
    if(!weak_ptr.expired())
    {
        shared_ptr<int> lock = weak_ptr.lock();
        if(lock)
        {
            cout << *lock << endl; // 5
        }
    }
}
```
    5

## unique_ptr
서로 공유하지 않고 하나만 할당하는 ptr에 사용되며, 자동으로 할당해제되는 용도로 사용