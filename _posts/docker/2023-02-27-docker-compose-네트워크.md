---
layout: single
title: "🐳 Docker Compose 네트워크"
categories: [Docker]
tag: [Docker, Docker Compose, Container]
author_profile: true
toc: true
toc_sticky: true
---

💾 여러 개의 컨테이너로 구성된 Docker Compose 애플리케이션 내에서 컨테이너 간의 통신을 설명합니다.

<br/>

## 디폴트 네트워크
기본적으로 `Docker Compose`는 하나의 디폴트 네트워크에 모든 컨테이너를 연결합니다. 디폴트 네트워크의 이름은 `docker-compose.yml`가 위치한 디렉토리 이름 뒤에 `_default`가 붙습니다. 예를 들어, 디렉토리 이름이 `our_app`라면 디폴트 네트워크 이름은 `our_app_default`가 됩니다.
```shell
## /path/our_app 경로
$ docker-compose up -d

Creating network "our_app_default" with the default driver
Creating our_app_db_1 ... done
Creating our_app_web_1 ... done
```
`Docker Compose`로 애플리케이션을 내릴 때는 반대 순서로 먼저 컨테이너를 종료/제거해놓고 제일 마지막에 네트워크를 제거합니다.
```shell
$ docker-compose down
Stopping our_app_web_1 ... done
Stopping our_app_db_1  ... done
Removing our_app_web_1 ... done
Removing our_app_db_1  ... done
Removing network our_app_default
```
<br/>
<br/>



## 컨테이너 간 통신

**같은 네트워크 간 통신**  
같은 네트워크 안에서 컨테이너 간의 통신은 호스트명을 사용합니다. 만약 호스트명을 지정하지 않았다면 서비스의 이름이 호스트명이 됩니다.
```shell
$ docker-compose exec web ping db

PING db (192.168.48.2) 56(84) bytes of data.
64 bytes from our_app_db_1.our_app_default (192.168.48.2): icmp_seq=1 ttl=64 time=0.094 ms
64 bytes from our_app_db_1.our_app_default (192.168.48.2): icmp_seq=2 ttl=64 time=0.162 ms
```
<br/>

**호출 위치에 따른 통신**  
컨테이넌 간 통신에서 주의할 점은 접속하는 위치가 디폴트 네트워크 내부냐 외부냐에 따라서 포트(port)가 달라질 수 있다는 것입니다.
```shell
services:
  web:
    build: .
    ports:
      - "8001:8000"
```
```shell
# 호스트 컴퓨터에서 web 서비스 컨테이너 접속
$ curl -I localhost:8001

HTTP/1.1 200 OK
Date: Fri, 05 Jun 2020 02:05:10 GMT
Server: WSGIServer/0.2 CPython/3.8.2
Content-Type: text/html
X-Frame-Options: DENY
Content-Length: 16351
X-Content-Type-Options: nosniff
```
```shell
# 같은 네트워크 내의 다른 컨테이너에서 web 서비스 컨테이너 접속
$ docker-compose exec alpine curl -I web:8000

HTTP/1.1 200 OK
Date: Fri, 05 Jun 2020 02:13:46 GMT
Server: WSGIServer/0.2 CPython/3.8.2
Content-Type: text/html
X-Frame-Options: DENY
Content-Length: 16351
X-Content-Type-Options: nosniff
```
<br/>
<br/>



## 커스텀 네트워크 추가
`Docker Compose`는 디폴트 네트워크 뿐만 아니라 다른 네트워크도 필요에 따라 추가할 수 있습니다.
```yaml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    networks: # 서비스에서 연결할 네트워크 작성
      - default
      - our_net

  db:
    image: postgres
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres

# 네트워크 추가
networks:
  our_net:
    driver: bridge
```

`Docker Compose`로 애플리케이션을 실행하면 두개의 네트워크가 생성되는걸 확인할 수 있습니다.
```shell
$ docker-compose up -d
Creating network "our_app_default" with the default driver
Creating network "our_app_our_net" with driver "bridge"
...

$ our_app docker network ls
NETWORK ID          NAME                   DRIVER              SCOPE
f1859120a0c3        bridge                 bridge              local
95b00551745b        host                   host                local
1f7202baa40a        none                   null                local
2682634e6535        our_app_default        bridge              local
525403b38bbe        our_app_our_net        bridge              local
```
`our_net`은 `Docker Compose` 내부에서 정의된 네트워크 이므로 애플리케이션을 내릴 때 디폴트 네트워크와 함께 삭제됩니다.
```shell
$ docker-compose down

Stopping our_app_web_1 ... done
Stopping our_app_db_1  ... done
Removing our_app_web_1 ... done
Removing our_app_db_1  ... done
Removing network our_app_default
Removing network our_app_our_net
```
<br/>
<br/>



## 외부 네트워크 사용
`Docker Compose`가 제공하는 디폴트 네트워크 대신에 외부에서 미리 생성해놓은 다른 네트워크를 사용할 수도 있습니다.

`our_net`이라는 네트워크 생성합니다.
```shell
$ docker network create our_net
6d791b927c8c151c45a10ac13c62f3571ecf38a90756fd2ca1c62b7d3de804e8
```
`docker-compose.yml`에서 네트워크의 옵션에 `our_net`네트워크에 `external`을 설정합니다.
```shell
# 1번 방벙
version: '3.7'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    networks:
      - our_net

networks:
  our_net:
    driver: bridge
    external: true
```
{% capture notice %}
**경고**  
외부에서 생성된 네트워크이므로 `Docker Compose` 애플리케이션을 내릴 때 해당 네트워크가 함께 삭제되지 않습니다.
```shell
$ docker-compose down

Stopping our_app_web_1 ... done
Stopping our_app_db_1  ... done
Removing our_app_web_1 ... done
Removing our_app_db_1  ... done
Network our_net is external, skipping
```

{% endcapture %}
<div class="notice--warning">{{ notice | markdownify }}</div>



## 레퍼런스 참고
- [docker-compose 네트워크](https://docs.docker.com/compose/networking/){:target="_blank"}
- [docker-compose 네트워크 설정](https://docs.docker.com/compose/compose-file/#network_mode){:target="_blank"}
<br/>
<br/>
<br/>
<br/>
<br/>


# 🙇🏻‍♂️ 참고사이트
- [https://www.daleseo.com](https://www.daleseo.com/?tag=Docker){:target="_blank"}