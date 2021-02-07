---
title: "homebrew로 H2 Databse 설치 및 사용"
permalink: /etc/h2-database-by-homebrew
excerpt: "homebrew로 H2 Databse 설치 및 사용ㅎㅏ는 방법입니다."
last_modified_at: 2021-02-04
categories:
  - etc
  - database
tags:
  - h2
  - db
  - databse
  - homebrew
author_profile: true
header:
  teaser: /assets/images/blog/2021/0001/teaser.png
---

# 설치
```bash
$ brew install h2
```

# 실행
```bash
$ h2
```

# 연결시 아래와 같은 오류 발생할 때
![](/assets/images/blog/2021/0001/image01.png)

## 원인
- test라는 데이터베이스가 존재하지 않기 때문.

## 해결
- 아래와 같이 설정한 후 ~~[연결시험]~~이 아닌 **[연결]**을 실행한다.
    - 설정
      - Generic H2 (Server)
      - JDBC URL: jdbc:h2:~/test
      - 사용자명: sa
      - 비밀번호: (없음)
- 이렇게 연결을 하면 home directory에 db 파일이 생성된다.
    - ~/test.mv.db
- 연결이 성공한 후에 원래 접속 방법을 시도하면 성공하게 된다.
    - JDBC URL: jdbc:h2:tcp://localhost/~/test
