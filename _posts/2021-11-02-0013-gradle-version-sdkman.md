---
title: "SDKMAN을 이용한 Gradle 버전관리"
permalink: /sdkman/gradle
excerpt: "SDKMAN을 이용하여 Gradle의 여러 버전을 사용한다."
last_modified_at: 2021-11-02
categories:
  - sdkman
  - gradle
tags:
  - sdkman
  - gradle
author_profile: true
header:
  teaser: /assets/images/blog/2021/0013/teaser.png
---

## SDKMAN?

- 유닉스 기반 시스템에서 여러가지 SDK의 병렬 버전을 관리하기 위한 도구

### 설치

```bash
$ curl -s "https://get.sdkman.io" | bash
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
$ sdk version

==== BROADCAST =================================================================
* 2020-12-10: springboot 2.2.12.RELEASE available on SDKMAN!
* 2020-12-10: leiningen 2.9.5 available on SDKMAN!
* 2020-12-08: kotlin 1.4.21 available on SDKMAN!
================================================================================

SDKMAN 5.9.1+575
```

### Gradle 버전 목록

```bash
$ sdk list gradle

================================================================================
Available Gradle Versions
================================================================================
     6.8-rc-1            5.6                 4.3                 2.5
     6.7.1               5.5.1               4.2.1               2.4
     6.7                 5.5                 4.2                 2.3
     6.7-rc-5            5.4.1               4.1                 2.2.1
     6.7-rc-4            5.4                 4.0.2               2.2
     6.7-rc-3            5.3.1               4.0.1               2.1
     6.7-rc-2            5.3                 4.0                 2.0
     6.7-rc-1            5.2.1               3.5.1               1.12
     6.6.1               5.2                 3.5                 1.11
     6.6                 5.1.1               3.4.1               1.10
     6.5.1               5.1                 3.4                 1.9
     6.5                 5.0                 3.3                 1.8
     6.4.1               4.10.3              3.2.1               1.7
     6.4                 4.10.2              3.2                 1.6
     6.3                 4.10.1              3.1                 1.5
     6.2.2               4.10                3.0                 1.4
     6.2.1               4.9                 2.14.1              1.3
     6.2                 4.8.1               2.14                1.2
     6.1.1               4.8                 2.13                1.1
     6.1                 4.7                 2.12                1.0
     6.0.1               4.6                 2.11                0.9.2
     6.0                 4.5.1               2.10                0.9.1
     5.6.4               4.5                 2.9                 0.9
     5.6.3               4.4.1               2.8                 0.8
     5.6.2               4.4                 2.7                 0.7
     5.6.1               4.3.1               2.6

================================================================================
+ - local version
* - installed
> - currently in use
================================================================================
```

### 설치하기

```bash
$ sdk install gradle 4.8
```

### 설치된 버전 삭제

```bash
$ sdk uninstall gradle 4.8
```

### 설치된 버전 변경

```bash
$ sdk use gradle 5.0
```

### 현재 사용 버전 확인

```bash
$ sdk current

Using:

gradle: 4.8
```
