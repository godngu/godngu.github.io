---
title: "자바스크립트 함수 코드 해석 순서"
permalink: /javascript/function-code-translate
excerpt: "자바스크립트의 함수코드 해석 순서를 코드를 통해 살펴보자"
last_modified_at: 2021-10-08
categories:
  - javascript
tags:
  - javascript
author_profile: true
header:
  teaser: /assets/images/blog/2021/0007/teaser.png
---

## 예제 코드

```jsx
function book() {
  debugger;
  var title = "hello";
  function getBook() {
    return title;
  }
  var readBook = function () {
    getBook();
  };
}
book();
```

1. book() 호출
2. loop1, 함수 선언문 해석
   1. `function getBook() {};`
3. loop2, 변수 초기화
   1. `var title = undefined;`
   2. `var readBook = undefined;`
4. loop3, 코드 실행
   1. `var title = 'hello';`
   2. `var readBook = function() {};`
   3. `getBook();`

## 함수 선언문 해석

1. 마지막 줄에서 book() 함수를 호출함.
2. 엔진 제어가 book 함수의 첫 번째 줄로 이동
   1. `debugger`를 실행하지 않는다.
3. 함수 안에서 함수 선언문을 찾는다.
   1. 위에서 아래로 내려가면서 하나씩 검색
4. `function getBook() {}`이 함수 선언문이므로 **function 오브젝트를 생성**한다.
5. 더 이상 함수 선언문이 없으므로 다시 함수의 첫 번째 줄로 이동한다.

## 변수 초기화

1. `debugger`를 실행하지 않는다.
2. `var title = 'hello';`
   1. `title` 변수에 `undefined`를 할당한다.
   2. `'hello'`를 할당하지 않는다.
3. `function getBook() {}`은 초기화를 했으므로 초기화하지 않는다.
4. `var readBook = function() {};`
   1. `readBook` 변수에 `undefined`를 할당한다.
   2. **함수 표현식은 변수를 선언만 한다.**
5. 초기화 단계가 끝나면 함수의 첫 번째 줄로 이동한다.

## 코드 실행

1. debugger를 실행하면서 실행이 멈춘다.
2. `var title = 'hello'`
   1. `title` 변수에 `'hello'`를 할당한다.
3. `function getBook() {return title};`
   1. 실행이 아닌 선언이므로 다음 줄로 이동한다.
4. `var readBook = function() {};`
   1. function 오브젝트를 생성하여 `readBook` 변수에 할당한다.
   2. readBook이 function 오브젝트가 되므로 이제 readBook 함수를 호출할 수 있다.
5. `getBook()` 함수를 호출한다.
   1. 같은 방법으로 `getBook()` 함수의 함수와 변수를 초기화하고 코드를 실행한다.

함수 선언문 해석 → 변수 초기화(undefined) → 코드 실행 순서로 해석해야 한다.
만약 변수 초기화 시점에서 함수 선언문에 의해 function object가 할당되어 있다면 초기화 되지 않는다.
