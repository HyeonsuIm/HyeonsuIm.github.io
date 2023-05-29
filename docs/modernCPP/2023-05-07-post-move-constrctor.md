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

# allocator
C++ STL을 사용하다 보면 성능이나 외부 라이브러리 연결 등 다양한 목적을 위해 할당 방식을 변경해야 하는 경우가 있다. 이 경우 allocator를 사용하여 STL에서의 할당/해제를 관리할 수 있다.

STL의 allocator를 하기 전에 아래와 같은 사전 지식이 필요하다.
## new 호출 시 동작
C++에서는 new를 호출할 경우 호출된 객체의 size를 operator new로 전달하여 메모리를 할당한다.
그 후, 해당 객체의 constructor를 호출하는 동작을 한다. 정리하면 아래와 같다.
1. operator new -> 메모리 할당(malloc)
2. constructor -> 생성자 호출

```c++
#include <iostream>

using namespace std;

class Test
{
public:
    Test()
    {
        cout << "Test()" << endl;
    }

    void* operator new(size_t size)
    {
        cout << "malloc " << size << endl;
        return malloc(size);
    }

    int value;
};

int main()
{ 
    Test* temp = new Test;
    Test* t = (Test*)operator new(sizeof(Test));
}
```

```console
malloc 4
Test()
```

## Placement new
이미 할당당되어있는 메모리를 사용하기 위해 생성자를 호출한다.
```c++
#include <iostream>

using namespace std;

class Test
{
public:
    Test()
    {
        value=3;
        cout << "Test()" << endl;
    }

    void* operator new(size_t size)
    {
        cout << "new " << size << endl;
        return malloc(size);
    }

    void* operator new(size_t size, void* p)
    {
        cout << "placement new " << endl;
        return p;
    }

    int value;
};

int main()
{ 
    Test* t = (Test*)Test::operator new(sizeof(Test));
    cout << t->value << endl;

    new(t) Test;

    cout << t->value << endl;
}
```
```console
new 4
0
placement new 
Test()
3
```


기본적으로 아래의 4가지 함수가 정의되어야 한다.
* 할당(operator new)
  * 데이터를 할당
  * 할당된 데이터는 다른 allocator에서도 해제하는데 문제가 없어야 한다.
* 해제(delete)
  * 데이터를 해제
* 생성(construct)
  * 할당된 영역에 데이터를 초기화
* 소멸(destory)
  * 할당된 영역에 데이터를 제거

그리고 자료구조를 구성하기 위해 rebind라는 특수한 형태를 사용한다. rebind는 아래와 같이 정의하며, 실제 데이터 외에 자료구조 구성을 위한 추가적인 데이터를 allocator에서 같이 할당하기 위해서 사용한다. ex) linked list의 next ptr
```c++
template<typename T>
class MyAllocator
{
    template<typename U>
    struct rebind{
        typedef MyAllocator<U> other;
    };
    ...
}
```

custom allcoator는 아래와 같이 작성할 수 있다.
```c++
#include <iostream>
#include <vector>

using namespace std;

template<typename T>
class MyAllocator
{
public:
    typedef T value_type;
    typedef value_type* pointer;
    typedef const value_type* const_pointer;
    typedef value_type& reference;
    typedef const value_type& const_reference;
    typedef std::size_t size_type;
    typedef std::ptrdiff_t difference_type;

    template<typename U>
    struct rebind{
        typedef MyAllocator<U> other;
    };

public:
    inline explicit MyAllocator(){};
    inline ~MyAllocator(){};
    inline explicit MyAllocator(MyAllocator const&){};
    template<typename U>
    inline explicit MyAllocator(MyAllocator<U> const&){};

    inline pointer address(reference r){return &r;}
    inline const_pointer address(const_reference r){return &r;}

    inline pointer allocate(size_type cnt, typename std::allocator<void>::const_pointer = 0)
    {
        cout << "alloc " << cnt << endl;
        return reinterpret_cast<pointer>(::operator new(cnt * sizeof(T)));
    }
    inline void deallocate(pointer p, size_type cnt){
        cout << "delete " << cnt << endl;
        ::operator delete(p);
    }

    inline size_type max_size() const{
        return INT_MAX / sizeof(T);
    }

    inline void construct(pointer p, const T& t){new(p)T(t);}
    inline void destory(pointer p){p->~T();}
    
    inline bool operator==(MyAllocator const&) {return true;}
    inline bool operator!=(MyAllocator const& a) {return !operator==(a);}

    private:
    
};

int main()
{ 
    vector<int, MyAllocator<int>> v;
    for(int i=0;i<3;i++)
    {
        cout << "push" << endl;
        v.push_back(i);
    }
    for(int i=0;i<3;i++)
    {
        cout << "pop" << endl;
        v.pop_back();
    }
}
```