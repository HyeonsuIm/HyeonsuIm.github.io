---
layout: default
parent: typescript
nav_order: 5
title: "TypeScript"
last_modified_at: 2023-02-12T12:00:00+09:00
categories:
  - TypeScript
tags:
  - TypeScript
---

# Type Script란?

javascript의 모든 내용을 포함(superset)하는 언어로, javascript의 단점들을 보완하여 만든 언어이다. 컴파일 과정을 거쳐 동적인 javascript에서 발생할 수 있는 에러를 사전에 방지해주고, 컴파일하여 javascript 파일을 생성해준다.


<br><br>
  

# 환경 설정

1. npm을 사용한 방법
   1. npm 설치(node.js 설치)
   2. npm install -g typescript
2. VScode 플러그인 설치

위의 환경 설정이 끝나면, 아래의 명렁어로 typescript를 컴파일할 수 있다.

    tsc example.ts

아래의 명령어로 ts파일이 변경될 경우 자동으로 컴파일하도록 watch option을 줄 수도 있다.

    tsc example.ts -w

<br><br>

# 문법

기본적인 문법은 javascript와 동일하며, 컴파일 시 에러를 찾기 위하여 자료형들을 명시적으로 사용할 수 있다.
<br>




* 자료형
  * number
  * string
  * boolean
  * any
    * 어떤 자료형으로도 대체될 수 있으며, 자료형은 typeof로 구분할 수 있다.
    * ex. ```typeof val === "number"```
  * unknown
  * array
    * 초기화 시 하나의 타입으로 정의되는 경우, 하나의 타입에 대한 배열로 사용된다.
    * 초기화할 때 여러 타입으로 정의되는 경우, any 타입에 대한 배열로 사용된다.
  * object
  * union
    * 여러 자료형을 사용할 수 있는 자료형

아래와 같이 명시적으로 type을 선언할 수 있고, 선언된 type과 맞지 않는 경우 컴파일 에러가 발생하게 된다.
```
let a=10;             // number
let b:number;         // number

let c="abc";           // string
let d:string;         // string

let e=true;           // boolean
let f:boolean;        // boolean

let g=['a','b'];      // array[string]
let h:string[] = [];  // array[string]

let i=[1,'a'];        // array[any]
let j:any[] = [];     // array[any]

let k={name:'a'};     // object
let k={name:string};  // object
let l:object;         // object
```

함수는 아래와 같이 선언할 수 있다.
```
// number를 파라미터로 받는 함수
const func1 = (value:number){
}

// array를 파라미터로 받는 함수
const func2 = (values:any[]){
}

// number이라는 변수를 가진 object를 파라미터로 받는 함수
const func2 = (values:{value:number}[]){
}
```
