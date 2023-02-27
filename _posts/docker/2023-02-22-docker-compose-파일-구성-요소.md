---
layout: single
title: "🐳 Docker Compose 파일 구성 요소"
categories: [Docker]
tag: [Docker, Docker Compose, Container]
author_profile: true
toc: true
toc_sticky: true
---

💾 도커 컴포즈의 기본 구성 요소룰 정리한 내용입니다. 자주 사용하는 항목만 정리하였고, 다른 항목들을 확인하시려면 [docker-compose](https://docs.docker.com/compose/compose-file){:target="_blank"}를 확인하세요.

<br/>

# Docker compose란?
Docker Compose를 이용하면 여러 개의 컨테이너(container)로 구성된 애플리케이션을 하나의 파일(YAML)에 정의해놓고 명령어를 사용해 한 번에 올리거나 내릴 수 있습니다. Docker Compose는 기본적으로 `docker-compose.yml` 파일을 설정 파일로 사용합니다.
<br/>
<br/>


## 기본 구조
`docker-compose.yml` 파일은 대략적으로 다음과 같은 구조를 갖습니다.
```yaml
version: "3.7"
services:
  web:
    # 컨테이너 서비스, 웹 애플리케이션 설정
  db:
    # 컨테이너 서비스, 데이터베이스 설정
networks:
  # 네트워크 설정
volumes:
  # 볼륨 설정
```
Docker Compose에서 서비스는 독립된 컨테이너에서 돌아가는 애플리케이션의 구성 요소라고 생각하면 됩니다. 가장 먼저 프로젝트에서 개발하고 있는 애플리케이션 자체가 서비스가 될 것이고, 그 밖에 해당 애플리케이션이 의존하는 데이터베이스 등도 서비스가 될 수 있습니다. 즉, 메인 애플리케이션 뿐만 아니라 정상적으로 구동되기 위해서 필요한 기반 시스템까지 Docker Compose로 설정한다고 보시면 됩니다. 
<br/>
<br/>

## .env 파일
`docker-compose.yml` 파일과 같은 경로에 `.env` 파일을 정의할 수 있습니다. `.env `파일에 환경변수를 정의 하면 `docker-compose.yml` 에서 사용할 수 있습니다.
```text
# .env

MY_ENV="prod"
```
```yaml
# docker-compose.yml
version: '3.7'
services:
  web:
    build:
      context: web/
      args:
        PROFILE: ${MY_ENV}
```
<br/>
<br/>
<br/>
<br/>



# 구성 요소
`docker-compose.yml`작성시 자주 사용하는 항목을 정리한 내용입니다.
<br/>

## build
`build` 항목은 해당 서비스의 이미지를 빌드하기 위한 용도로, `Dockerfile`이 위치하는 경로를 지정하기 위해 사용됩니다. 
```yaml
## docker-compose.yml 파일과 동일한 디렉토리에 위치한 Dockerfile 을 사용해서 web 서비스의 이미지를 빌드
version: '3.7'
services:
  web:
    build: .
```
```yaml
## Dockerfile이 아닌 다른 이름의 파일로 빌드를 하고 싶거나, 빌드 인자를 넘겨야 하는 경우
version: '3.7'
services:
  web:
    build:
      context: ./app
      dockerfile: Dockerfile-dev
      args:
        arg1: "development"
	    arg2: "docker"
```
<br/>
<br/>


## image
프로젝트에서 직접 개발하지 않는 데이터베이스와 같은 경우에는, 이미지를 직접 빌드하는 대신에 이미지 저장소(repository)로 부터 이미지를 내려받아서 사용하는 것이 일반적입니다.
```yaml
# image 항목은 이미지 저장소로 부터 내려받을 이미지의 이름과 태그를 명시하는데 사용됩
version: '3.7'
services:
  db:
    image: postgres:13
  cache:
    image: redis
```
<br/>
<br/>


## ports
`ports` 항목은 외부로 노출시킬 포트의 맵핑을 명시합니다. 바인드(bind)가 필요한 호스트 외부 포트와 컨테이너 내부 포트를 지정합니다.
```yaml
services:
  web:
    ports:
      - "8000:8000"
  db:
    ports:
      - "5432:5432"
```
<br/>
<br/>



## volumes
`volumes` 항목은 볼륨 설정을 위해 쓰입니다. 마운트(mount)가 필요한 호스트의 경로와 컨테이너의 경로를 명시합니다.
```yaml
# 호스트의 경로로 볼륨 설정
services:
  web:
    volumes:
      - .:/web
```
```yaml
# 미리 생성한 docker volume으로 볼륨 설정
services:
  frontend:
    image: node:lts
    volumes:
      - myapp:/home/node/app

volumes:
  myapp:
    external: true
```
<br/>
<br/>



## depends_on
`depends_on` 항목은 서비스 간 의존 관계를 지정하기 위해서 사용됩니다. 예를 들어, 웹 애플리케이션이 올라오기 전에 데이터베이스, 레디스 서비스가 먼저 올라와야 한다면 다음과 같이 설정합니다.
```yaml
version: "3.7"
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```
<br/>
<br/>



## command
`command` 항목은 해당 서비스가 올라올 때 `Dockerfile` 의 `CMD` 명령문을 무시하고 실행할 명령어를 설정하기 위해서 사용됩니다.
```yaml
services:
  web:
    command: node .
```
<br/>
<br/>


## environment
`environment` 항목은 환경 변수를 설정하기 위해서 사용됩니다.
```yaml
services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: wordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
```
```yaml
# 아래와 같은 형식으로도 사용할 수 있습니다.
environment:
  - RACK_ENV=development
  - SHOW=true
```
<br/>
<br/>



## 레퍼런스 참고
[docker-compose](https://docs.docker.com/compose/compose-file){:target="_blank"}
<br/>
<br/>
<br/>
<br/>
<br/>


# 🙇🏻‍♂️ 참고사이트
- [https://www.daleseo.com](https://www.daleseo.com/?tag=Docker){:target="_blank"}