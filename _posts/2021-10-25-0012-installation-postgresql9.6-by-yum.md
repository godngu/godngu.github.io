---
title: "PostgreSQL9.6 설치(yum)"
permalink: /postgresql/installation/yum
excerpt: "Yum으로 PostgreSQL9.6 설치하기"
last_modified_at: 2021-10-25
categories:
  - postgresql
tags:
  - postgresql
  - yum
author_profile: true
header:
  teaser: /assets/images/blog/2021/0012/teaser.png
---

## 설치 가능한 패키지 확인

```bash
$ sudo yum list postgres*
```

## 패키지 설치

```bash
$ sudo yum install -y postgresql-server.x86_64 postgresql-contrib.x86_64 postgresql-devel.x86_64
$ sudo yum install -y postgresql96-server.x86_64 postgresql96-contrib.x86_64 postgresql96-devel.x86_64
```

## 설치 확인

```bash
$ sudo yum list installed | grep postgres
postgresql.x86_64                     9.6.8-1.amzn2.0.2              @amzn2extra-postgresql9.6
postgresql-contrib.x86_64             9.6.8-1.amzn2.0.2              @amzn2extra-postgresql9.6
postgresql-devel.x86_64               9.6.8-1.amzn2.0.2              @amzn2extra-postgresql9.6
postgresql-libs.x86_64                9.6.8-1.amzn2.0.2              @amzn2extra-postgresql9.6
postgresql-server.x86_64              9.6.8-1.amzn2.0.2              @amzn2extra-postgresql9.6
```

## Data Directory

```bash
/var/lib/pgsql96
```

## service 확인

```bash
$ cd /etc/init.d
-rwxr-xr-x 1 root root  7949 12월  5  2018 postgresql96
```

## 초기화

```bash
$ sudo postgresql-setup initdb
```

```bash
# service 확인한 정보로 실행
$ sudo service postgresql96 initdb
```

## 시작

```bash
$ sudo systemctl start postgresql
$ sudo service postgresql96 start
```

## 부팅시 자동 시작 등록

```bash
$ sudo chkconfig postgresql on
$ sudo chkconfig postgresql96 on
```

## 이미 생성되어 있는 postgres 계정에 패스워드 추가(password: postgres)

```bash
$ sudo passwd postgres
```

## postgres 계정으로 접속

```bash
$ su - postgres
```

## postgres의 쉘 커맨드 실행

```bash
-bash-4.2$ psql postgres
```

## 유저생성, DB생성, DB권한설정

(예제)

```bash
postgres=# CREATE USER sonar WITH ENCRYPTED PASSWORD 'sonar';
CREATE ROLE
postgres=# CREATE DATABASE sonar OWNER sonar;
CREATE DATABASE
postgres=# ALTER ROLE sonar WITH createdb;
ALTER ROLE
postgres=# GRANT ALL PRIVILEGES ON DATABASE sonar TO sonar;
GRANT

# 쉘 커맨드를 종료합니다.
postgres-# \q

# 원래 계정으로 돌아갑니다.
-bash-4.2$ exit

# 재시작
$ sudo systemctl restart postgresql
```

## 처음 posrgresql 구성간 아래와 같은 에러 메시지가 나올 경우

### 오류

```bash
....
psycopg2.OperationalError: FATAL:  Ident authentication failed for user "test"
....

psql: FATAL:  Peer authentication failed for user "test"
```

### 방안

```bash
[root] # vi /var/lib/pgsql/10/data/pg_hba.conf
위 경로 들어가 상황에 맞게 METHOD 부분의 ident를  MD5 or trust로 변경해 준다.

# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```
