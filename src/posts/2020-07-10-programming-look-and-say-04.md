---
layout: post
title: Programming Look-and-Say 04 (TWIL)
categories: [programming]
tags: [programming]
---

# Iterator

## 개미 수열의 특성

- Haskell로 하면 100번째 줄을 출력할 수 있지만, Java로 하면 100번째 줄을 호출 할 때 메모리 용량 부족 에러가 발생
- 개미수열은 각 줄이 30%씩 길어지는 특성이 있다. 100줄 만하더라도 600기가 수준의 용량이 필요함
- 리스트를 나타내는 새로운 방법: **Iterator**

## 이터레이터와 이터러블

### 이터레이터

- 무언가를 순차적으로 하나씩 살펴보기 위한 범용적인 인터페이스
- 리스트나 트리처럼 값을 저장하지는 않음
- 자료 구조의 값을 순차적으로 참조할 수 있는 방법을 제공
- 자바스크립트의 이터레이터는 next() 메소트를 가짐
  - 호출할 때마다 값을 반환하거나 종료 여부를 알림

### 이터러블

- 이터레이터를 제공하는 컬렉션
- 자바스크립트의 이터러블 객체는 Symbol.iterator 키에 이터레이터를 반환하는 함수를 가짐
- 자바, 자바스크립트는 for문으로 이터러블을 순화할 수 있도록 문법적으로 지원

## 개미 수열과 이터레이터

- 50번째 줄의 100 번째 숫자를 계산하기 위해서는 49번 줄의 74번째 숫자만 알면된다
  - 49번 줄이나 50번 줄을 모두 계산할 필요가 없다
- 중요한 것은 순차적으로 하나씩만 '참조'할 수 있으면 되는 것. 이미 읽고 '처리'한 값들을 따로 '저장'할 필요가 없는 것
- 각 이터레이터는 마치 커서처럼 해당 줄의 테이터를 다음 이터레이터로 하나씩 전달한다
  - 따라서, 개미 수열 문제에서 핵심이 되는 것은 다음 줄을 계산하는 'next()' 함수가 구현하는 로직이 됨

## 자바스크립트: 이터레이터

- 개미 수열의 각 줄을 '이터레이터'로 나타낸다
  - 다음 줄을 계산하는 next() 함수는 앞 줄(이터레이터)를 입력 받아 다음 줄(이터레이터)를 만든다
- 자바스크립트의 이터레이터 생성 함수 뼈대

```js
function makeIterator() {
  return {
    next() {
      return { done: true };
    }
  };
}
```

```js
function ant(n) {
    let s = iter([1])
    for (let i = 0; i < n; i ++) {
        s = next(s);
    }
    return s;
}

function next(ns) {
    return concat(map(g => iter([g.length, g[0]], group(ns)));
    }
```

### map

- 이터레이터(변환함수) => 새로운 이터레이터
- 입력 이터레이터가 종료될 때 출력 이터레이터도 종료
- 값은 1 대 1 매칭

```js
function map(f, it) {
  return {
    //이터레이터(객체)를 반환한다
    next() {
      //반환하는 이터레이터의 next() 함수
      let { value, done } = it.next(); //파라미터로 전달받은 이터레이터의 next()를 호출한다
      if (done) {
        return { done: true };
      } else {
        return { done: false, value: f(value) };
      }
    }
  };
}
```

### concat

- 중첩된 이터레이터를 순환

```js
function concat(it) {
  let inner = null;
  return {
    next() {
      while (true) {
        if (inner === null) {
          let { value, done } = it.next();
          if (done) {
            return { done: true }; // no more inner iterator
          } else {
            inner = value;
          }
        }
        let { value, done } = inner.next(); // inner(이터레이터)의 next()를 호출
        if (done) {
          inner = null;
        } else {
          return { done: false, value };
        }
      }
    }
  };
}
```

### group()

```js
function group(it) {
  let g = null;
  return {
    next() {
      while (true) {
        let { value, done } = it.next();
        switch (true) {
          case done && g === null: {
            return { done: true };
          }
          case done: {
            let result = g;
            g = null;
            return { done: false, value: result };
          }
          case g === null: {
            g = [value];
            break;
          }
          case g[0] === value: {
            g.push(value);
            break;
          }
          default: {
            let result = g;
            g = [value];
            return { done: false, value: result };
          }
        }
      }
    }
  };
}
```

## iter(), uniter()

### iter()
- 이터러블을 이터레이터로 만듬
```js
function iter(obj) {
    return obj[Symbol.iterator]();
}
```

### uniter()
- 이터레이터를 이터러블로 만듬
```js
function uniter(it) {
    return {
        [Symbol.iterator]: function() {
            return it;
        }
    }
}
```

## 지연 리스트로서의 이터레이터
- 지연리스트
    - 한 번에 모두 담을 수 없는, 사실상 무한의 길이를 가진 리스트?!
    - 스트림(stream)이라고 부르기도 함
- 이터레이터는 지연 리스트 혹은 무한 리스트의 일종
    - 이터레이터의 next()를 호출할 때까지 리스트의 꼬리 부분은 계산이 지연된다


## 이터레이터 구현 리뷰: 이터레이터의 구현이 복잡한 이유
 - 이터레이터가 루프의 제어권을 가지고 있지 않다
 - 이터레이터가 자체적으로 상태 정보를 가져야 하는 상황이 오면 구현이 쉽지 않다
 - 이터레이터 구현이 복잡해 지는 경우가 빈번해서 언어 차원에서 다른 방법을 제공
      - 제네레이터
