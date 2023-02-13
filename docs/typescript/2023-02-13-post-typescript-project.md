---
layout: default
parent: typescript
nav_order: 5
title: "TypeScript project"
last_modified_at: 2023-02-14T00:30:00+09:00
categories:
  - TypeScript
tags:
  - TypeScript
---

# 프로젝트 typescript관리

프로젝트 내에서 아래의 명령어를 입력하면 tsconfig.json파일이 생성된다.
tsconfig.json파일을 이용하여 컴파일 옵션을 설정하고, 프로젝트 단위로 typescript를 관리할 수 있다.
```
tsc --init
```


<br><br>
  

# 컴파일 옵션 설정
tsconfig.json에는 다양한 옵션이 있으며 이를 바탕으로 여러가지 컴파일에 대한 설정을 변경할 수 있다.

```
{
  "compilerOptions":{
    target
    module
    rootDir
    outDir
  }
  include
}
```