---
title: "Javascript 호이스팅 예제"
permalink: /javascript/hoisting
excerpt: "예제를 통해 javascript의 호이스팅 동작 순서를 확인한다."
last_modified_at: 2021-10-21
categories:
  - javascript
tags:
  - javascript
  - hoisting
author_profile: true
header:
  teaser: /assets/images/blog/2021/0009/teaser.png
---

- 함수 선언문은 초기화 단계에서 function 오브젝트를 생성하므로 어디에서도 함수를 호출할 수 있다.
- 초기화 단계에서 값이 있으면 초기화 하지 않는다.

```jsx
var result = book();
console.log(result);

function book() {
  return "A";
}

book = function () {
  return "B";
};
console.log(book());
```

1. 첫 번째 루프: **함수 선언문을 찾는다.**
   1. function book(){}을 발견하고 [[scope]]에 {book: function Object}가 등록된다.
   2. book = function() {}는 변수를 할당 하므로 패스한다.
2. 두 번째 루프: **변수 초기화**
   1. {result: `undefined`}로 초기화 한다.
   2. function book() {};는 함수 선언문이므로 패스 한다.
   3. book = function() {}는 변수 할당이므로 {book: undefined}로 초기화 하려고 시도한다.
      1. 하지만 book에는 이미 function Object가 할당되어 있으므로 undefined로 초기화 하지 않는다.
3. 세 번째 루프: 실행
   1. var result = book(); 에서 book()함수를 호출한다.
      1. book은 function object이므로 호출이 가능해진다.
      2. 함수 호출 후에는 {result: "A"}가 할당된다.
   2. 로그에 찍힌다.
   3. book 변수에 새로운 function Object가 할당된다.
   4. 마지막 로그에 "B"가 찍힌다.

## 함수 선언문, 함수 호출, 함수 선언문

```jsx
function book() {
  function getBook() {
    return "a";
  }
  console.log(getBook()); // b
  function getBook() {
    return "b";
  }
  //console.log(getBook());  // b
}
book();
// 결과: b
```

- 함수 선언문에 의해서 `[[scope]]`에 `{getBook: function object}`가 할당된다.
- 두 번째 함수 선언문에 의해서 `[[scope]]`에 `{getBook: function object}`가 할당된다.
- **결과적으로 `getBook`에는 두 번째 함수 선언문이 적용**되어 있다.

## 함수 표현식, 함수 호출, 함수 표현식

```jsx
function book() {
  var getBook = function () {
    return "a";
  };
  console.log(getBook()); // a
  var getBook = function () {
    return "b";
  };
  //console.log(getBook());  // b
}
book();
// 결과: a
```

- 첫 번째 루프에서 함수 선언문을 찾지만 없으므로 패스된다.
- 두 번째 루프에서 첫 번째 함수 표현식에 의해 `{getBook: undefined}`가 할당된다.
- 두 번째 루프에서 두 번째 함수 표현식에 의해 `{getBook: undefined}`가 할당된다.
- 세번째 루프에서 `getBook`에 `function object`가 할당되므로 함수 실행이 가능해지고 'a' 결과가 찍힌다.
- 세번째 루프에서 `getBook`에 새로운 `function object`가 할당되므로 함수 실행이 가능해지고 마지막 로그에서 'b' 결과가 찍힌다.

## 함수 선언문, 함수 호출, 함수 표현식

```jsx
function book() {
  function getBook() {
    return "a";
  }
  console.log(getBook()); // a
  var getBook = function () {
    return "b";
  };
  //console.log(getBook());  // b
}
book();
// 결과: a
```

- 첫 번째 루프에서 함수 선언문에 의해서 `[[scope]]`에 `{getBook: function object}`가 할당된다.
- 두 번째 루프가 돌때 함수 표현식에 의해 `getBook`에 `undefined`를 할당하려고 하지만
  이미 `function object`가 할당되어 있으므로 `var getBook` 변수는 초기화 되지 않는다.
- 세 번째 루프에서 `getBook`에는 이미 `function object`가 할당되어 있으므로 함수 실행이 가능해지고 'a' 결과가 찍힌다.
- 세 번째 루프에서 함수 표현식에 의해 `getBook`에 새로운 `function object`가 할당되고, 마지막 로그는 'b'가 출력된다.

## 함수 표현식, 함수 호출, 함수 선언문

```jsx
function book() {
  var getBook = function () {
    return "a";
  };
  console.log(getBook()); // a
  function getBook() {
    return "b";
  }
  //console.log(getBook());  // a
}
book();
// 결과: a
```

- 첫 번째 루프에서 함수 선언식을 발견하고 `[[scope]]`에 `{getBook: function object}`가 할당된다.
- 두 번째 루프에서 함수 표현식에 의해 `getBook`에 `undefined`를 할당하려 하지만 이미 `function object`가 생성되어 있으므로 초기화 되지 않는다.
- 세 번째 루프에서 함수 표현식에 의해 `**var getBook`에 새로운 function object가 반영\*\*된다. 따라서 로그는 'a'가 출력된다.
- 세 번째 루프에서 함수 선언문은 패스된다.
- 결과적으로 `getBook`에는 함수 표현식에서 할당된 `function object`가 있으므로 마지막 로그는 'a'가 찍힌다.
