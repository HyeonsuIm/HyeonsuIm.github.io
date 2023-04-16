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




# Logging

아래와 같이 설정하여 로그를 저장할 수 있습니다.
```nginx
server {
  access_log <경로>;
  error_log <경로>;
}
```

일반적으로 아래와 같이 설정합니다.
```nginx
server {
  access_log /var/log/nginx/<server name>/access.log;
  error_log /var/log/nginx/<server name>/error.log;
}
```

# Log 상세 설정
## 로깅 포맷 변경
기본적으로 로그 포맷은 아래와 같고, 변경할 수 있습니다.
```nginx
# 기본 로그 포맷
log_format default_log  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

# custom 로그 정의
log_format  custom_log  '$remote_addr - $remote_user [$time_local] [$request_time] '
                  '"$request" $status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

# custom 로그 설정
access_log <경로> custom_log
```

로그 예약어
```nginx
# 예약어
$remote_user # HTTP Authorization 접속시 접속한 유저
$remote_addr # 방문자 IP
$time_local # 요청을 처리한 서버 시간
$status # HTTP 응답코드
$request # 클라이언트 요청
$http_user_agent # 클라이언트가 사용한 브라우저
$http_referer # 해당 페이지 이전에 거쳐온 URL
$body_bytes_sent # 보낸 데이터 크기 (byte)
$http_x_forwarded_for # 프록시 서버를 거치기 전의 접속 IP
$request_time # 요청을 처리하는데 걸린시간
$connection # 로그를 남길 당시의 커넥션 수
```

아래와 같이 json형태로 로그를 출력할 수도 있습니다.
```nginx
log_format json_logging escape=json
 '{'
   '"time_local":"$time_local",'
   '"remote_addr":"$remote_addr",'
   '"request":"$request",'
   '"status": "$status",'
   '"body_bytes_sent":"$body_bytes_sent",'
   '"request_time":"$request_time",'
   '"http_referrer":"$http_referer",'
   '"http_user_agent":"$http_user_agent"'
 '}';
```

## 로깅 레벨 설정
로그에는 다양한 레벨이 있고, 레벨에 맞춰 로그를 출력할 수 있습니다.
```nginx
# 로그 레벨
info - 정보
notice - 정상
warn - 경고
error - 프로세스 처리 에러
crit - 프로새스에 영향을 주는 오류
alert - 프로새스에 심각한 영향을 주는 오류
emerg 서비스 불가 수준 오류 - 설정, syntax등이 잘못된 경우
debug 디버그 정보
```


```nginx
error_log <경로> <로그 레벨>
```