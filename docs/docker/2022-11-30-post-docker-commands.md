---
layout: default
parent: docker
nav_order: 2
title: "도커 명령어"
last_modified_at: 2022-11-30T08:00:00+09:00
categories:
  - Docker
tags:
  - Docker
  - Docker Commands
---


# 도커 버전 확인
현재 설치된 도커의 버전을 확인하는 명령어

    # docker -v
    Docker version 20.10.3, build 55f0773

<br><br>

# 도커 실행 및 삭제
도커를 실행시키는 명령어.
만약 이미지가 없다면 이미지를 다운받아서 실행한다.

도커를 실행할 때 사용하는 명령어는 아래와 같다.
* docker pull
  * 도커 이미지를 다운받는다.
* docker create
  * 도커 컨테이너를 생성한다.
* docker start
  * 도커 컨테이너를 시작한다. 
* docker attach
  * 도커 컨테이너 내부로 진입한다.

<br>

실제로 사용되는 명령어는 아래 두 가지이며, 위 명령어의 조합으로 되어있다.
* docker create
  * pull > create
* docker run
  * pull > create > start > attatch 

<!--
<br>

실행 시 사용되는 옵션들은 아래에서 확인할 수 있다.
* [docker run options](2022-11-30-post-docker-run-options.md)
-->
<br>

실행중인 도커 컨테이너를 중지/삭제할 때 사용하는 명령어는 아래와 같다.
* docker stop
  * 실행중인 도커 컨테이너를 를 중지시킨다.
* docker rm
  * 실행중이지 않은 도커 컨테이너를 삭제한다.

<br><br>

# 도커 정보 확인
현재 다운받아져 있는 이미지를 확인하는 명령어
    
    # docker images
    REPOSITORY                    TAG       IMAGE ID       CREATED         SIZE
    mysql                         latest    3842e9cdffd2   13 days ago     538MB


<br><br>

# 도커 볼륨
도커 컨테이너에서 공유하는 데이터를 저장하는 곳으로, 컨테이너가 삭제되더라도 데이터는 유지된다.

볼륨 생성

    # docker volume create myvolume  
    myvolume

볼륨 삭제

    # docker volume rm myvolume
    myvolume

볼륨 확인

    # docker volume ls
    DRIVER    VOLUME NAME
    local     myvolume

볼륨 상세정보 확인

    # docker volume inspect myvolume
    [
        {
            "CreatedAt": "2022-11-30T08:58:19+09:00",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "/volume1/@docker/volumes/myvolume/_data",
            "Name": "myvolume",
            "Options": {},
            "Scope": "local"
        }
    ]