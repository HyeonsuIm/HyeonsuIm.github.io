---
layout: default
parent: project
nav_order: 1
title: "rocket.chat 채팅 사용기"
last_modified_at: 2023-04-15T21:00:00+09:00
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
rocket.chat 자체적으로는 HTTPS를 지원하지 않기 때문에, 중간에 nginx를 이용하여 HTTPS를 처리하도록 하였습니다.
아래와 같이 nginx docker를 생성하여 HTTPS요청을 받은 뒤, 이를 rocket.chat 서버로 전달해주도록 처리하면 HTTPS가 정상적으로 처리됨을 확인할 수 있었습니다.<br>

HTTPS를 지원하기 위해서 /etc/letsencryp/www.domain.com:3001/ 폴더에 인증서 파일을 추가하여야 합니다.

```nginx
# nginx.conf
# Upstreams
upstream backend {
    server localhost:3000;
}

# HTTPS Server
server {
    listen 3001 ssl;
    server_name www.domain.com;
    server_tokens off;
    # You can increase the limit if your need to.
    client_max_body_size 200M;

    error_log /var/log/nginx/rocketchat.access.log;
	
    ssl_certificate /etc/letsencrypt/live/www.domain.com:3001/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/www.domain.com:3001/privkey.pem;
	ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    location / {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;

        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header X-Nginx-Proxy true;

        proxy_redirect off;
    }
}
```
```yml
#compose.yml
  nginx:
    build: ./nginx
    container_name: rocketchat_nginx
    restart: always
    ports:
      - "3001:3001"
    volumes:
      - /etc/rocketchat/certbot/conf:/etc/letsencryp
```