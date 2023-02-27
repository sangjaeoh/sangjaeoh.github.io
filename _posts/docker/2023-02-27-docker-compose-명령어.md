---
layout: single
title: "🐳 Docker Compose 명령어"
categories: [Docker]
tag: [Docker, Docker Compose, Container]
author_profile: true
toc: true
toc_sticky: true
---

💾 Docker Compose 애플리케이션을 제어하기 위해 사용되는 명령어를 설명합니다.
<br/>


## 파일 지정
`Docker Compose`는 기본적으로 커맨드가 실행하는 디렉토리에 있는 `docker-compose.yml` 또는 `docker-compose.yaml`를 설정 파일로 사용합니다. 다른 이름이나 경로의 파일을 Docker Compose 설정 파일로 사용하고 싶다면 `-f` 옵션으로 명시를 해줍니다.
```shell
# -f 옵션으로 파일일 지정할 수 있습니다.
$ docker-compose -f docker-compose-local.yml up

# -f 옵션은 여러 개의 설정 파일을 사용할 때도 사용할 수 있습니다.
# 이 때는 나중에 나오는 설정이 앞에 나오는 설정보다 우선하게 됩니다.
$ docker-compose -f docker-compose.yml -f docker-compose-test.yml up
```
<br/>


## 컨테이너 생성/실행
`docker-compose up` 커맨드는 `Docker Compose`에 정의되어 있는 모든 서비스 컨테이너를 한 번에 생성하고 실행하기 위해서 사용합니다.
```shell
# -d 옵션을 사용하여 백그라운드에서 컨테이너를 띄웁니다.
# -d 옵션을 사용하지 않으면 현재 터미널에 로그가 출력되고, Ctrl+c를 사용하여 종료할 수 있습니다.
$ docker-compose up -d
```
<br/>


## 컨테이너 정지/삭제
`docker-compose down` 커맨드는 `Docker Compose`에 정의되어 있는 모든 서비스 컨테이너를 한 번에 정지시키고 삭제합니다.
```shell
# -v 옵션을 주면 docker-compose.yml에 정의된 볼륨을 삭제하며 down 합니다.
$ docker-compose down -v
```
<br/>


## 컨테이너 시작
`docker-compose start` 커맨드는 내려가 있는 있는 특정 서비스 컨테이너를 올리기 위해서 사용합니다. `docker-compose up` 커맨드를 사용해도 내려간 서비스를 알아서 올려줍니다.
```shell
$ docker-compose start web
```
<br/>


## 컨테이너 정지
`docker-compose stop` 커맨드는 돌아기고 있는 특정 서비스 컨테이너를 정지시키기 위해서 사용합니다.
```shell
$ docker-compose stop web
```
<br/>


## 컨테이너 조회
`docker-compose ps` 커맨드는 `Docker Compose`에 정의되어 있는 모든 서비스 컨테이너 목록을 조회할 때 사용합니다.
```shell
$ docker-compose ps

      Name                    Command               State           Ports
----------------------------------------------------------------------------------
django-app_db_1    docker-entrypoint.sh postgres    Up      5432/tcp
django-app_web_1   python manage.py runserver ...   Up      0.0.0.0:8000->8000/tcp
```
<br/>


## 컨테이너 로그
`docker-compose logs` 커맨드는 서비스 컨테이너의 로그를 확인하고 싶을 때 사용하며, 보통 `-f` 옵션을 붙여서 실시간 로그를 확인합니다. `-f` 다음에 서비스명을 입력하면 해당 서비스의 로그만 볼 수 있습니다.
```shell
$ docker-compose logs -f web

web_1  | May 30, 2020 - 22:16:29
web_1  | Django version 3.0.6, using settings 'our_project.settings'
web_1  | Starting development server at http://0:8000/
web_1  | Quit the server with CONTROL-C.
```
<br/>


## 명령어 전달
`docker-compose exec` 커맨드는 실행 중인 서비스 컨테이너를 대상으로 어떤 명령어를 날릴 때 사용합니다.
```shell
$ docker-compose exec -it web /bin/bash
```
<br/>


## 일회성 명령어
`docker-compose run` 커맨드는 서비스 컨테이너의 특정 명령어를 일회성으로 실행할 때 사용합니다.
```shell
$ docker compose run web bash
```
<br/>



## 설정 확인
`docker-compose config` 커맨드는 `Docker Compose` 설정을 확인할 때 사용합니다. `-f` 옵션으로 여러 개의 설정 파일을 사용할 때, 최종적으로 어떻게 설정이 적용되는지 확인해볼 때 유용합니다.
```shell
$ docker compose config
```
<br/>


## 레퍼런스 참고
[docker-compose](https://docs.docker.com/compose/reference/){:target="_blank"}
<br/>
<br/>


## 🙇🏻‍♂️ 참고사이트
- [https://www.daleseo.com](https://www.daleseo.com/?tag=Docker){:target="_blank"}