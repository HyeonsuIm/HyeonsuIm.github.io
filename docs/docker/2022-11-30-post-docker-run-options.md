---
layout: default
parent: docker
nav_order: 3
title: "도커 시작 옵션"
last_modified_at: 2022-11-04T23:00:00+09:00
categories:
  - Docker
tags:
  - Docker
  - Docker Commands
---

# 도커 옵션

    $ docker run (<옵션>) <이미지> (<명렁어>) (<인자>)

도커 실행 명령어는 위와 같으며, 실제 사용되는 다양한 옵션들은 아래와 같습니다.

* -d
  * 컨테이너를 백그라운드로 실행
* -it
  * 컨테이너를 실행하고, 쉘로 접속
* --name
  * 컨테이너의 이름을 정의
* -e
  * 환경변수 정의
* -p
  * 접근할 수 있는 포트 정의
* -w
  * WORKDIR 설정
* --entrypoint
  * ENTRYPOINT 설정
* -rm
  * 컨테이너가 종료될 때, 관련 리소스를 삭제