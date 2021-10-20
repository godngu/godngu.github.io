---
title: "Vuex의 store 기본 사용법"
permalink: /javascript/vuejs-vuex-store
excerpt: "예제를 통해 vuex의 store 기본 사용법을 확인한다."
last_modified_at: 2021-10-20
categories:
  - vue.js
  - vuex
  - store
tags:
  - vue.js
  - vuex
  - store
author_profile: true
header:
  teaser: /assets/images/blog/2021/0008/teaser.png
---

## 기본 문법

- `state`에 값을 저장한다.
- `mutations`로 state의 값을 변경한다.
  - `commit` 함수를 사용한다.
  ```javascript
  this.$store.commit("함수명", 변수값);
  this.$store.commit("setUsername", "Something text");
  ```
- `getters`로 값을 가져온다.
- mutations, getters에서는 **기본 인자로 state를 받는다.**
  ```javascript
  getters: {
      functionA(state) {}
  },
  mutations: {
      functionB(state) {}
  }
  ```

## 시나리오

- **로그인 컴포넌트와 헤더 컴포넌트간데 데이터를 공유한다.**
- 로그인을 완료하면 메인 페이지로 이동하고, 헤더에 사용자 정보가 나온다.
  ![image01.png](/assets/images/blog/2021/0008/image01.png)
- 이때 로그인, 회원가입 버튼은 숨김 처리 한다.
  - `v-if`, `v-else`
    ![image01.png](/assets/images/blog/2021/0008/image02.png)

## 코드

### vuex 설치

```bash
$ npm i vuex
```

### store 생성

```javascript
/* /src/store/index.js */
import Vue from "vue";
import Vuex from "vuex";

Vue.use(Vuex);

export default new Vuex.Store({
  state: {
    username: "",
  },
  getters: {
    isLogin(state) {
      return state.username !== "";
    },
  },
  mutations: {
    setUsername(state, username) {
      state.username = username;
    },
  },
});
```

- `state`로 값을 공유한다.
- `mutations`로 state의 값을 변경한다.
- `getter`로 state의 값을 꺼낸다.

### 로그인 컴포넌트

- 로그인이 완료되면 사용자 정보를 `store`의 `state`에 등록한다.
  - 이때 `mutations`을 호출한다.

```javascript
this.$store.commit("setUsername", data.user.username);
```

### 헤더 컴포넌트

- `store`의 `state`에 등록된 사용자 정보를 `getters`를 이용해서 꺼낸다.
- `computed` 속성을 이용하여 로직을 정리한다.
  - `isUserLogin`
- 화면상의 분기 처리는 `<template>`를 사용한다.

```html
<template>
  <header>
    <div>
      <router-link to="/" class="logo"> TIL </router-link>
    </div>
    <div class="navigations">
      <template v-if="isUserLogin">
        <span class="username">{{ $store.state.username }}</span>
      </template>
      <template v-else>
        <router-link to="/login">로그인</router-link>
        <router-link to="/signup">회원가입</router-link>
      </template>
    </div>
  </header>
</template>
```

```jsx
export default {
  computed: {
    isUserLogin() {
      return this.$store.getters.isLogin;
    },
  },
};
```
