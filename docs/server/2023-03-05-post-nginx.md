---
layout: default
parent: sginx
nav_order: 1
title: "Nginx"
last_modified_at: 2023-04-15T21:00:00+09:00
categories:
  - Nginx
tags:
  - Nginx
---

# Nginx란?
클라이언트와 직접 연결되는 서버로서, 전달받은 요청을 적절한 작업 서버에 분배해주고 결과를 클라이언트에 전달해주는 중간 역할 서버입니다. 이를 이용하여 로드 밸런서로서의 역할도 하게 됩니다.
![Nginx](/assets/images/nginx_flow.png)

# 세팅 법

nginx.conf파일로 설정을 한 후, nginx를 실행하면 됩니다.

localhost 20000번 포트로 전달받은 /(root)에 대한 요청을 파이썬으로 구성된 19999번 포트로 전달하는 설정입니다.

```nginx
# nginx.config
server {
        listen 20000;
        server_name localhost;
        client_max_body_size 1M;

        location / {
                include uwsgi_params;
                uwsgi_pass localhost:19999;
        }
}
```

# 설정파일 세팅
설정 파일을 세팅할 때는 2가지 타입의 제어문이 있습니다.

## single directive
제어문과 입력값으로 서버 환경을 설정합니다.

* user
  * 실행할 프로세스의 권한을 설정한다.
  * 없을 경우 root로 설정된다.
* worker_processses
  * 작업할 프로세스 개수를 설정한다.
* listen
  * 수신을 대기할 port를 설정한다.
* include
  * 외부 conf파일을 추가할 때 사용한다.
* client_max_body_size
  * 수신받을 요청의 최대 body 크기를 설정한다.

## block directive
block을 바탕으로 복합적인 서버 환경을 설정합니다.
* http
  * 루트의 개념으로, 서버의 기본값에 대한 설정에 사용한다.
  * 하위에 server와 location블록을 가질 수 있다.
* server
  * 여러 서버를 사용할 때, 각각의 서버에 대한 환경을 설정합니다.
* location block
  * 각각의 호출 경로에 따라 다른 처리가 필요할 경우 사용한다.
* events
  * 네트워크와 관련된 동작 방법을 설정하는 블록이다.




# Log

