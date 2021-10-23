---
title: "SonarQube 설치 및 젠킨스 연동"
permalink: /sonarqube
excerpt: "Jenkins, SonarQube, sonar-scanner, slack 연동하기"
last_modified_at: 2021-10-22
categories:
  - sonarqube
  - jenkins
tags:
  - sonarqube
  - sonarscanner
  - jenkins
author_profile: true
header:
  teaser: /assets/images/blog/2021/0010/teaser.png
---

## SonarQube 정보

- 소스코드 품질관리
- SonarQube 7.7
  - https://docs.sonarqube.org/7.7/requirements/requirements/
  - https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.7.zip
- Open JDK 8
- Database
  - PostgreSQL 9.3+
  - Oracle 11G+
  - MySQL 5.6+

## 아키텍쳐

![image01.png](/assets/images/blog/2021/0010/image01.png)

## OpenJDK8 설치

## PostgreSQL9.6 설치

## SonarQube7.7 설치

### 다운로드

```bash
$ wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.7.zip
```

### 압축해제, 디렉터리 이동

```bash
$ unzip sonarqube-7.7.zip
$ sudo mv sonarqube-7.7 /opt/sonarqube
```

### 계정 설정

- 실행할 때 root 계정으로 실행하면 안된다.
- 그러므로 sonar라는 계정을 생성해서 진행한다.

#### 유저 생성

```bash
# 그룹 생성
$ sudo groupadd sonar

# 유저 생성
$ sudo useradd -m -g sonar -c "SonarQube User" -d /opt/sonarqube -s /bin/bash -p sonar sonar
# -m 홈디렉토리생성, -g 그룹지정, -c 설명, -d 디렉토리 지정, -s 쉘지정, -p 패스워드 지정
```

#### 소유권한 변경

```bash
# 파일 소유권한 변경
$ sudo chown -R sonar:sonar /opt/sonarqube
```

### 환경설정

#### 부팅 서비스로 등록(자동 실행)

```bash
$ sudo vim /usr/lib/systemd/system/sonar.service

# 아래 내용을 추가한다.
[Unit]
Description=SonarQube Service
After=syslog.target
After=network.target

[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
ExecReload=/opt/sonarqube/bin/linux-x86-64/sonar.sh restart

[Install]
WantedBy=multi-user.target
```

#### 데몬 재로드를 한뒤 부팅시 등록 및 실행

```bash
# 데몬 재로드를 한뒤 부팅시 등록 및 실행
$ sudo systemctl daemon-reload
$ sudo systemctl enable sonar
$ sudo systemctl start sonar
```

#### db 연결 설정

```bash
$ su - sonar
# 만약 인증 실패가 발생하면 root에서 접속을 시도한다.
# root 접속이 안되면 root 패스워드를 활성화 해야 한다.(sudo passwd root)

$ vim /opt/sonarqube/conf/sonar.properties
```

![image03.png](/assets/images/blog/2021/0010/image02.png)

![image03.png](/assets/images/blog/2021/0010/image03.png)

#### 실행 유저 설정

```bash
$ vim /opt/sonarqube/bin/linux-x86-64/sonar.sh
```

![image04.png](/assets/images/blog/2021/0010/image04.png)

#### 실행

```bash
$ -bash-4.2$ /opt/sonarqube/bin/linux-x86-64/sonar.sh console start
```

최초 계정

- admin / admin

### 처음 posrgresql 구성간 아래와 같은 에러 메시지가 나올 경우

#### 오류

```bash
....
psycopg2.OperationalError: FATAL:  Ident authentication failed for user "test"
....

psql: FATAL:  Peer authentication failed for user "test"
```

#### 방안

```bash
[root] # vi /var/lib/pgsql/10/data/pg_hba.conf
위 경로 들어가 상황에 맞게 METHOD 부분의 ident를  MD5 or trust로 변경해 준다.

# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

## Sonar Scanner 설치

- 젠킨스 서버에 설치한다.

### 다운로드

```bash
$ wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.2.0.1873-linux.zip
```

### 압축해제, 이동

```bash
$ unzip sonar-scanner-cli-4.2.0.1873-linux.zip
$ sudo mv sonar-scanner-4.2.0.1873-linux/ /opt/sonar-scanner
```

### conf 폴더의 sonar-project.properties 수정

- SonarQube 서버가 같은 vpn내에 있다면 private ip를 넣는다.

```yaml
#----- Default SonarQube server
# sonarQube 를 / 가 아닌 별도의 context 로 설정했을 경우 localhost 대신 Full URL 로 설정 필요(Ex: https://qa.example.com/sonarqube)
sonar.host.url=http://localhost:9000
#----- Default source code encoding
sonar.sourceEncoding=UTF-8
```

## Jenkins 설정

- https://www.youtube.com/watch?v=wSNlFThmUNs

### Plugin 설치

![image05.png](/assets/images/blog/2021/0010/image05.png)

### 보안토큰 생성

- sonarqube 웹페이지 접속
  ![image06.png](/assets/images/blog/2021/0010/image06.png)

![image07.png](/assets/images/blog/2021/0010/image07.png)

### Jenkins관리 > 시스템 설정 > SonarQube servers 설정

![image08.png](/assets/images/blog/2021/0010/image08.png)

### 토큰 적용

![image09.png](/assets/images/blog/2021/0010/image09.png)

### Jenkins관리 > Global Tool Configuration

- Jenkins에 설치한 sonar-scanner를 설정한다.
  ![image10.png](/assets/images/blog/2021/0010/image10.png)

### SonarQube 프로젝트 생성

![image11.png](/assets/images/blog/2021/0010/image11.png)

![image12.png](/assets/images/blog/2021/0010/image12.png)

### src의 project root에 sonar-project.properties 생성

![image13.png](/assets/images/blog/2021/0010/image13.png)

```properties
sonar.projectKey=웹에서 생성한 프로젝트 Key
sonar.projectName=웹에서 생성한 프로젝트 Name
sonar.projectVersion=1.0


sonar.source=src/main/java
sonar.java.binaries=buils.classes


sonar.language=java
sonar.sourceEncoding=UTF-8
```

### Jenkins Job 설정

- Build > Add build step > Execute SonarQube Scanner
- 위에서 추가한 sonar-project.properties 파일을 넣는다.

![image14.png](/assets/images/blog/2021/0010/image14.png)

## Report

![image15.png](/assets/images/blog/2021/0010/image15.png)

![image16.png](/assets/images/blog/2021/0010/image16.png)

## Slack Notification

- https://github.com/kogitant/sonar-slack-notifier-plugin
  - 다운로드: https://github.com/kogitant/sonar-slack-notifier-plugin/releases/

### Plugin 설치

```bash
$ cd /opt/sonarqube/extensions/plugins
$ wget https://github.com/kogitant/sonar-slack-notifier-plugin/releases/download/2.1.2/cks-slack-notifier-2.1.2.jar

# 재부팅
$ /opt/sonarqube/bin/linux-x86-64/sonar.sh restart
```
