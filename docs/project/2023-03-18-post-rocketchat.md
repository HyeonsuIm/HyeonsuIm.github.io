---
layout: default
parent: project
nav_order: 1
title: "rocket.chat 채팅 사용기"
last_modified_at: 2023-03-19T10:50:00+09:00
categories:
  - rocket.chat
tags:
  - Docker
  - nginx
  - RocketChat
---

# rocket.chat이란 ?

오픈소스를 기반으로 한 채팅 프로그램으로, 보안상의 사유 등으로 다른 채팅 프로그램을 사용할 수 없는 경우 자체 서버를 사용한 채팅을 할 수 있다는데 그 장점이 있습니다. 
채팅 클라이언트는 안드로이드/IOS/Win32 등의 OS에서 바로 받을 수 있으며, 서버는 rocketchat에서 제공하는 방법에 따라 자체적으로 구성하여 사용하게 됩니다.. 
개인용/기업용 등 용도에 따라 비용이 구분되며, 개인용 서버 사용은 무료입니다.

<br>

# rocket.chat 서버 세팅

아래 페이지에 기재된 방법을 따라 rocket.chat서버를 세팅할 수 있습니다.

> https://docs.rocket.chat/deploy/prepare-for-your-deployment

<br>
저는 자체 Synology Server가 있어 Docker를 이용하여 서버를 구성하였습니다.
우선 아래와 같이 rocket.chat서버 구성을 위한 docker_compose를 다운로드 받았습니다.

```
curl -L https://raw.githubusercontent.com/RocketChat/Docker.Official.Image/master/compose.yml -O 
```
<br>

그리고 compose.yml과 동일 폴더 내에 .env파일을 생성하여 RELEASE 버전과 ROOT_URL을 설정 해 주었습니다.<br>
```
$ cat .env
RELEASE=5.4.3
ROOT_URL=http://localhost:3000 
```

<br>

설정을 완료한 후 아래의 명령어를 실행하니 서버가 동작하였습니다.
```
docker-compose up -d
```


<br>

# rocket.chat 클라이언트 세팅

클라이언트는 rocket.chat에서 제공하는 클라이언트를 다운받아서 서버와 연결하면 바로 사용할 수 있었습니다.

> 참조 : https://docs.rocket.chat/deploy/installing-client-apps


<br>

# HTTPS



# Truble shuting



# 결과