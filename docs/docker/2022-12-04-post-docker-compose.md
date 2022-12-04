---
layout: default
parent: docker
nav_order: 5
title: "도커 컴포즈"
last_modified_at: 2022-12-04T21:00:00+09:00
categories:
  - Docker
tags:
  - Docker
  - DockerCompose
---

# 도커 컴포즈란?

여러 개의 컨테이너의 옵션과 환경을 정의한 파일로, 컨테이너를 순차적으로 생성하는데 사용됩니다.
일반적으로 하나의 서비스 내에서 여러개의 컨테이너를 생성해야할 때 사용됩니다.


<br><br>

# 도커 컴포즈 명렁어

* docker-compose up
  * docker-compse 서비스들을 실행시키는 명렁어
* docker-compose scale mysql=2
  * docker-compose의 컨테이너 수를 조절하는 명령어
* docker-compose ps
  * docker-compose의 컨테이너 목록을 확인하는 명렁어
* docker-compose down
  * docker-compose 서비스들을 중지시키는 명령어

<br><br>


# 도커 컴포즈 파일 작성

YAML 확장자로 도커 컴포즈를 작성하며, YAML 파일은 버전 정의, 서비스 정의, 볼륨 정의, 네트워크 정의의 4가지로 구성됩니다.

1. 버전 정의 : YAML 파일 맨 윗부분에 명시하며, YAML 파일 포맷 버전을 기입합니다.

    예시:

        version: '3.0'
2. 서비스 정의 : 생성할 컨테이너들의 옵션을 정의합니다.

    * image
      * 서비스의 컨테이너를 생성할 이미지를 선택합니다.
    * links
      * docker run의 --link와 같으며, 다른 서비스를 접근가능하도록 설정합니다.
    * environment
      * docker run의 -e와 같으며, 컨테이너 내부에서 사용할 환경변수를 지정합니다.
    * command
      * docker run의 마지막 커맨드와 같으며, 컨테이너가 실행될 때 수행할 명령어를 설정합니다.
      * Dockerfile의 RUN과 같은 배열 형태로도 사용 가능합니다.
    * depends_on
      * 컨테이너 간의 의존관계를 나타내며, 컨테이너 간 실행 순서에 영향을 줍니다.
    * ports
      * docker run의 -p와 같으며, 개방할 포트를 설정합니다.
      * 단일 호스트 환경에서 특정 포트를 사용하게 되면 scale 명령으로 컨테이너를 추가할 수 없습니다.
    * build
      * 미리 정의된 Dockerfile에서 이미지를 빌드해서 컨테이너를 생성하도록 합니다.
    * extends
      * 다른 YAML 파일에서 서비스 속성을 상속받게 설정합니다.
  
    <br>
    예시:

        services:
          my_container_1:
            image:mysql
            environment:
              - MYSQL_ROOT_PASSWORD=mypassword
            ports:
              - 3306:3306
          my_container_2:
            image:flask
            links:
              - mysql
            depends_on
              - mysql

3. 네트워크 정의

    도커 컴포즈는 기본적으로 브리지 타입의 네트워크를 생성합니다. 하지만 YAML파일에서 다른 네트워크를 사용하도록 설정할 수 있습니다.
    * driver
      * 브리지 네트워크가 아닌 다른 네트워크를 사용하도록 설정합니다.
      * 특정 드라이버에 필요한 옵션은 하위 항목으로 driver_ops로 전달할 수 있습니다.
    * ipam
      * IPAM(IP Address Manager)를 위해 사용하는 옵션으로, subnte, ip 범위 등을 설정할 수 있습니다.
      * driver에는 IPAM를 지원하는 드라이버를 입력해야 합니다.
    * external
      * 기존에 정의된 네트워크를 사용하도록 설정합니다.

    <br>
    예시 :

        network:
          mynetwork:
            driver: overlay

4. 볼륨 정의

    볼륨은 아무 설정도 하지 않을 경우 local로 설정됩니다. 하지만 아래와 같이 별도의 볼륨을 정의하여 사용할 수 있습니다.
    * driver
      * 볼륨을 생성할 때 사용될 드라이버를 정의합니다. 
      * driver_opts로 추가적인 설정을 할 수 있습니다.
    * external
      * 프로젝트마다 볼륨을 생성하지 않고, 기존의 볼륨을 사용하도록 설정합니다.

    <br>
    예시 :

        volumes:
          myvolume:
            external:true

# YAML 파일 검증하기

docker-compose config 명렁어를 사용하여 현재 디렉토리의 docker-compose.yml파일을 검사할 수 있습니다.

특정 경로 파일을 검사하려면 docker-compose -f <파일 경로> config로 검사할 수 있습니다.