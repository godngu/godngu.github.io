---
title: "AWS CodeDeploy에서 환경변수 적용하는 방법"
permalink: /aws/codedeploy-envrionment-variable
excerpt: "AWS CodeDeploy를 통한 배포시 서버를 재기동 하는 과정에서 환경변수를 사용할 수 있는 방법을 제시합니다."
last_modified_at: 2020-04-09
image:
  teaser: /assets/images/blog/0003/teaser.png
categories:
  - aws
tags:
  - aws
  - CodeDeploy
  - 환경변수
  - environment variable
#author_profile: true
header:
  teaser: /assets/images/blog/0003/teaser.png
---

## 환경변수를 사용 한 이유
##### 일반적인 Executable jar 실행
``` bash
$ java -jar application.jar
```

##### 필자의 상황
``` bash
$ java -Dspring.profiles.active=dev -Djasypt.encryptor.password=key -jar application.jar
```
> - `spring.profiles.active`
> 스프링 부트 프로파일 정보(local, dev, production)
> - `jasypt.encryptor.password`
> DB 패스워드를 암호화 하기 위한 jasypt key

#### 문제점
> jar를 위와 같이 실행시킬 경우 몇 가지 문제점이 있습니다.

1. 명령어가 길어서 `실수를 유발` 합니다.
2. 테스트를 하거나 여러 대의 서버에 설정할 경우 `번거롭습니다.`
3. 서버 재기동 shell이 프로젝트 내에 존재할 경우 `중요 정보가 노출`될 수 있습니다.

**제가 구축한 배포 환경에서는 서버의 재기동 쉘을 프로젝트 내에 두었습니다.**

![](/assets/images/blog/0003/img01.png)


### 일반적인 환경변수 사용
> `.bashrc`에 등록해서 사용합니다.

##### 문제점
- CodeDeploy는 `.bashrc`에 있는 환경변수를 읽어들이지 못합니다.

## CodeDeploy에서 환경변수 사용
> `/etc/.profile.d/codedeploy.sh`에 환경 변수를 등록하면 CodeDeploy가 읽어 들일 수 있습니다.

```bash
# /etc/profile.d/codedeploy.sh

export SPRING_PROFILES_ACTIVE=dev
export JASYPT_ENCRYPTOR_PASSWORD=key
```
