---
title: "코드 스플리팅을 이용한 Lazy loading"
permalink: /vuejs/code-splitting-lazy-loading
excerpt: "Code Splitting을 적용하여 컴포넌트를 Lazy Loading 함으로써 성능 향상"
last_modified_at: 2021-10-07
categories:
  - vuejs
tags:
  - vuejs
  - ssh
author_profile: true
header:
  teaser: /assets/images/blog/2021/0006/teaser.png
---

## 개요

- `/src/routes/index.js`에 라우터 설정을 한다.
  - `component` 속성에서 `*.vue`를 연결한다.
- 이렇게 라우터 설정을 하면 초반에 라우터에 설정된 모든 페이지(보여지지 않을 페이지 까지)를 가져오므로 성능에 문제가 생긴다.
- 실제 **라우터 path의 호출이 발생할 때 import를 통해서 자원을 호출**한다면 효율적이다.
  - **Lazy Loading**

## 코드

- `component: () => import('경로')`

```jsx
/* file: /src/routes/index.js */

import Vue from "vue";
import VueRouter from "vue-router";

Vue.use(VueRouter);

export default new VueRouter({
  mode: "history",
  routes: [
    {
      path: "/",
      redirect: "/login",
    },
    {
      path: "/login",
      component: () => import("@/views/LoginPage.vue"),
    },
    {
      path: "/signup",
      component: () => import("@/views/SignupPage.vue"),
    },
    {
      path: "/main",
      component: () => import("@/views/MainPage.vue"),
    },
    {
      path: "/add",
      component: () => import("@/views/PostAddPage.vue"),
    },
    {
      path: "*",
      component: () => import("@/views/NotFoundPage.vue"),
    },
  ],
});
```
