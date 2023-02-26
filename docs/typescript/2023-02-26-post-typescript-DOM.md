---
layout: default
parent: typescript
nav_order: 5
title: "TypeScript DOM"
last_modified_at: 2023-02-26T19:30:00+09:00
categories:
  - TypeScript
tags:
  - TypeScript
  - DOM
---

# typescript에서 DOM 사용

기본적으로는 javascript와 동일하게 사용할 수 있으며, 컴파일 시점에 명확하게 하게 위해 몇가지 추가해야할 것이 있다.

1. null 확인 또는 null이 아님을 명시<br>
   document의 객체를 가져온 경우 typescript에서는 html코드를 모르기 때문에 null일 수 있음을 에러로 알림.
   ```typescript
    const obj = document.querySeletor('a');
    console.log(obj.href); // => null check error

    // 해결 법.
    // 1. null 체크 후 사용
    const obj = document.querySeletor('a');
    if(obj){
      console.log(obj.href); // => OK
    }

    // 2. null이 아님을 명시(!, non-null assertion operator)
    const obj2 = document.querySeletor('a')!;
    console.log(obj2.href); // => OK
    ```
2. 명시적으로 객체를 정의<br>
   1번 예시에서는 'a' 태그를 가져왔기 때문에 객체의 타입을 컴파일 시점에 정의할 수 있다. 하지만 아래와 같은 경우 객체의 타입을 컴파일 시점에 정의 할 수 없다.
   ```typescript
   const obj = document.querySelector('.a_item')!;
   console.log(obj.href); // => error

   const obj2 = document.querySelector('.a_item') as HTMLAnchorElement;
   console.log(obj.href); // => OK
3. 