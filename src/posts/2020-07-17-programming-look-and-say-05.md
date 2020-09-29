---
layout: post
title: Programming Look-and-Say 05 (TWIL)
categories: [programming]
tags: [programming]
---

# Generator

- 이터레이터는 컬렉션의 요소들을 하나씩 접근하기 위한 인터페이스
- '값을 생성'하는 입장에서는 이터레이터를 만드는 것이 불편함
- 제네레이터는 이터레이터를 생성 관점에서 접근한 것
- 제네레이터는 yield를 만날 때까지 실행되어 '값을 생성' -> 전달 -> 멈춤
- 다시 값을 요청 받으면 멈춘 위치에서 실행을 재개해서 다음 yield까지 '값을 생성' -> 전달 -> 멈춤
- 실행을 멈추고 멈춘 위치에서 재개하는 것이 제네레이터의 가장 큰 특징
- generator 객체는 next() 메소트가 있다. 값을 출력한다
  - 값으로는 `{value, done}`이 출력된다. value는 값이고 done은 제네레이터가 종료되는지 알려준다

## javascript 제네레이터

- `function*`으로 선언됨
- yield로 값을 생성
- 제네레이터 객체는 이터레이터 프로토콜과 이터러블 프로토콜을 만족함

```js
function ant(n) {
  let s = gen([1]);
  for (let i = 0; i < n; i++) {
    s = next(s);
  }
  return s;
}

function* gen(obj) {
  yield* obj;
}

function* next(g) {
  //g는 제네레이터 객체. 한 줄의 모든 숫자들이 제네레이터의 value로 출력된다 (next()로 하나씩...)

  let keyNumber = g.next().value; //이터레이터 프로토콜
  // 바로 위 코드는 전 수열(g)의 첫 번째 숫자를 출력한다 --- <1>
  let count = 1;
  // 바로 위 코드는 <1>에서 첫 번째 숫자가 출력되고 이에 대한 갯수를 표현한다

  for (let value of g) {
    //이터러블 프로토콜
    // g가 next()를 통해서 하나씩 출력되는 값을 루프로, value라는 변수값으로 출력한다
    if (value === keyNumber) {
      count++;
      // next()로 출력된 value가 keyNumber와 같으면 count를 추가하고 다음 루프로 넘어간다 --- <2>
    } else {
      yield* [count, keyNumber]; // yield*: 다른 제네레이터 혹은 이터러블의 값을 모두 yield함
      // next()로 출력된 value가 keyNumber와 같지 않으면 지금까지 기록한 count와 keyNumber를 원소로 갖는 수열을 값으로 출력한다
      keyNumber = value;
      // value와 keyNumber가 같지 않으므로 이제는 keyNumber가 value가 된다
      count = 1;
      // 새로운 keyNumber가 생겼으니 count를 1로 만들어 다시 센다 --- <3>
    }
  }

  yield* [count, keyNumber];
  // <2>와 <3>에서는 yield를 하고 있지 않다. 따라서 남은 값(keyNumber와 count)이 존재한다?!
  // 남은 값을 내보내주기 위해서 yield를 마지막에 한번 더 해준다
}
```

### concat()

```js
function* concat(gs) {
  for (let g of gs) {
    yield* g;
    // gs의 value가 a, b라고 하고 a의 value가 1,2이고 b의 value가 3,4라고 하자
    // 일단 g는 a가 된다. 그리고 yield*이기 때문에 1과 2가 순서대로 출력된다
    // (만약 yield 였으면 그냥 a가 출력된다)
    // 그 뒤의 g는 b가 된다. 다시 yield*이기 때문에 3과 4가 순서대로 출력된다
    // (만약 yield 였으면 그냥 b가 출력된다)
    // 그렇게 1,2,3,4가 순서대로 출력된다
    // a와 b를 순서대로 합쳐서 출력하고 싶으면 gs라는 제네레이터 객체의 출력이 a,b가 되게 설정하고
    // for이라는 이터러블을 통해서 순차대로 출력하게 한다...?!
  }
}
```

### group()

```js
function* group(g) {
  let key = g.next().value;
  let count = 1;
  for (let value of g) {
    if (value === key) {
      count++;
    } else {
      yield* [count, key];
    }
  }
  yield* [count, key];
}
```

### map()

```js
function* map(f, g) {
  yield* f(g.next().value);
}
```

### concat, group, map
```js
function* gen(obj) {
    yield* obj;

}

function next(ns) {
    return concat(map(g => gen([g.length, g[0]]), group(ns)));
}
```

## 이터레이터/제네레이터 리뷰
 - 이터레이터/제네레이터를 사용하더라도 10000번째 줄을 출력할 수는 없다
     - 스택오버플로우, 메모리 에러
 - 제네레이터로 구현했을 때, 제네레이터 안에 제네레이터가 있다
     - 10000번째 줄을 구하려고 하면 동시에 10000개의 제네레이터가 호출된다
     - 스택오버플로우 발생!


## 제네레이터 이해하기
 - 제네레이터는 함수와 비슷하다
     - 하지만
     - 모든 값을 담는 배열을 만들고 한번에 반환하는 대신, 제네레이터는 값을 '한번에 하나씩만 생성'하여 전달한다
     - 제너레이터는 함수처럼 생겼지만 이터레이터처럼 동작한다
 - 제네레이터는 반쪽 코루틴
     - 코루틴의 특별한 케이스
     - 제네레이터는 '값을 생성'한 뒤에 언제나 호출자로 제어 흐름을 되돌린다
     - 코루틴은 제어 흐름을 이어갈 다른 코루틴을 지정한다


