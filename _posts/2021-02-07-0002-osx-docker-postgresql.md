---
title: "OS X + Docker + PostgreSQL"
permalink: /docker/osx-docker-postgresql
excerpt: "OS X에 도커를 이용하여 PostgreSQL DB를 설치하고 사용한다."
last_modified_at: 2021-02-07
categories:
  - etc
  - database
  - docker
tags:
  - docker
  - postgresql
  - db
  - databse
author_profile: true
header:
  teaser: /assets/images/blog/2021/0002/teaser.png
---
### 도커 프로세스 확인

```bash
$ docker ps -a
```

### 최신 버전의 PostgreSQL 설치

```bash
$ docker pull postgres
```

### 컨테이너 실행

```bash
$ docker run \
-p 5432:5432 \               # 로컬 포트 매핑
-e POSTGRES_USER=kil \       # 환경변수
-e POSTGRES_PASSWORD=1234 \
-e POSTGRES_DB=testdb \
--name pgsql \               # 컨테이너이름
-d \                         # 데몬으로 백그라운드에 띄움
postgres                     # 이미지 이름

```

프로세스 확인

```bash
$ docker ps
```

![](/assets/images/blog/2021/0002/image01.png)

### 도커 컨테이너에 들어가서 확인하기

```bash
$ docker exec -i -t pgsql bash
# -i: interactive mode
# -t: target container name
# bash: bash mode로 실행

# DB 접속
root@e40a0964d7a4:/# psql --username kil --dbname testdb
psql (13.1 (Debian 13.1-1.pgdg100+1))
Type "help" for help.

testdb=#
```

### DBeaver를 이용한 접속 테스트

- Host: localhost
- Port: 5432
- Database: testdb
- User: kil
- Password: 1234

### 컨테이너 종료하기

```bash
$ docker stop pgsql
```

종료된 컨테이너 실행하기

```bash
# CONTAINER_ID 확인
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
4fe0db0e1a65        postgres            "docker-entrypoint.s…"   7 minutes ago       Up 4 seconds        0.0.0.0:5432->5432/tcp   pgsql

# 실행
# docker start [CONTAINER_ID]
$ docker start 4fe0db0e1a65
```

### 컨테이너 삭제하기

```bash
$ docker rm pgsql
```

컨테이너만 종료했을 경우 컨테이너 내부에 있는 데이터는 그대로 남아 있지만
컨테이너 자체를 삭제하는 경우 해당 베이스 이미지만 그대로 사용하기 때문에 더이상 데이터가 남아 있지 않게 된다.

> 컨테이너 볼륨이나 로컬 컴퓨터에 데이터를 저장할 수 있는 공간을 따로 생성하여 외부에 저장할 수 있는 상태로 만들어야 한다.

- [https://www.daleseo.com/docker-volumes-bind-mounts/](https://www.daleseo.com/docker-volumes-bind-mounts/)

- [https://judo0179.tistory.com/96](https://judo0179.tistory.com/96)

### 볼륨 생성 및 조회

볼륨 생성

```bash
# docker volume create [이름]
$ docker volume create test-volume
```

볼륨 확인

```bash
$ docekr volume ls
```

상세정보 확인

- 볼륨이 생성된 경로를 확인할 수 있다.

```bash
$ docker volume inspect test-volume
```

### 볼륨을 컨테이너에 마운트하기

-v 옵션을 사용, 콜론(:)을 구분자로 해서 앞에는 마운트할 볼륨명, 뒤에는 컨테이너 내의 경로를 명시 해준다.

```bash
$ docker run \
-p 5432:5432 \
-e POSTGRES_USER=kil \
-e POSTGRES_PASSWORD=1234 \
-e POSTGRES_DB=testdb \
--name pgsql \
-d \
-v test-volume:/app    # 볼륨 마운트 (위치를 변경하면 DB가 올라오지 않는다;;)
postgres \
```

볼륨 삭제

```bash
$ docker volume rm test-volume
```

> 제거하려는 볼륨이 마운트되어 있는 컨테이너가 있을 때는 해당 볼륨이 제거되지 않는다.

```bash
$ docker rm -f [볼륨이름]
```

모든 볼륨 제거

```bash
$ docker volume prune
```
