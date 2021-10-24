---
title: "OpenJDK8 설치"
permalink: /openjdk8/installation
excerpt: "OpenJDK8 설치하기"
last_modified_at: 2021-10-23
categories:
  - openjdk
tags:
  - opnejdk
  - openjdk8
author_profile: true
header:
  teaser: /assets/images/blog/2021/0011/teaser.png
---

## 설치할 수 있는 자바 버전 확인

```bash
$ sudo yum list java*jdk*-devel
```

## 설치

```bash
$ sudo yum install -y java-1.8.0-openjdk-devel.x86_64
```

- -y: (모든 물음에 yes로 대답 하겠다는 옵션)

## 확인

```bash
[ec2-user@ip-10-31-4-30 ~]$ java -version
openjdk version "1.8.0_222"
OpenJDK Runtime Environment (build 1.8.0_222-b10)
OpenJDK 64-Bit Server VM (build 25.222-b10, mixed mode)

[ec2-user@ip-10-31-4-30 ~]$ javac -version
javac 1.8.0_222

[ec2-user@ip-10-31-4-30 ~]$ echo $JAVA_HOME

[ec2-user@ip-10-31-4-30 ~]$
```

## 환경변수 설정을 위한 절대경로 구하기

```bash
# root로 변경
$ sudo su

[root@ip-10-31-4-30 ec2-user]# javac -version
javac 1.8.0_222
[root@ip-10-31-4-30 ec2-user]# which javac
/bin/javac
[root@ip-10-31-4-30 ec2-user]# readlink -f /bin/javac
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.222.b10-0.amzn2.0.1.x86_64/bin/javac
```

## JAVA_HOME 설정

```bash
[root@ip-10-31-4-30 ec2-user]# vim /etc/profile

#JAVA_HOME
exprot JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.222.b10-0.amzn2.0.1.x86_64


[root@ip-10-31-4-30 ec2-user]# source /etc/profile
```

> 서버 재부팅!2021
