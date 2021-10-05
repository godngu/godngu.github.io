---
title: "Vue 컴포넌트 Pascalcase로 임포트 하기"
permalink: /github/vscode-vuejs-pascalcase-autocomplete
excerpt: "VSCode에서 vue.js의 components 임포트시 파스칼케이스로 임포트 하기 위한 설정."
last_modified_at: 2021-10-05
categories:
  - vscode
  - vuejs
  - vue.js
tags:
  - vscode
  - vuejs
  - vue.js
author_profile: true
header:
  teaser: /assets/images/blog/2021/0004/teaser.jpg
---

# Vue 컴포넌트 Pascalcase로 임포트 하기

## 증상

- VSCode의 `*.vue` 파일에서 컴포넌트 임포트시 kebabcase로 임포트 됨.

  ![image01.png](/assets/images/blog/2021/0004/image01.png)

## 목표

- kebacase대신 pascalcase로 임포트 하고자 함.

## 해결

### Vetur 플러그인의 설정 변경

> Settings → Vetur → Completion: Tab Casing ⇒ `initial`

![image02.png](/assets/images/blog/2021/0004/image02.png)

### 적용 결과

![image03.png](/assets/images/blog/2021/0004/image03.png)
