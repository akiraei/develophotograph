---
layout: post
title: decorator
categories: [programming, js]
tags: [programming, js]
---

# 데코레이터

## 자바스크립트의 데코레이터
- 데코레이터 '함수'
    - 고차 함수
- 데코레이터의 적용대상
    - class
    - class field (proposal)
        - variable
        - method

### class

```js
function isVenom(target) { //target은 class
    const propertyNames = Object.getOwnPropertyNames(target.prototype);

    if (!propertyNames.includes('poison')) {
        throw new Error('venom should be poison');
    }

    return target;
}

@isVenom
class Sweet {
    constructor(){
        this.taste = 'sweet'
        this.edible = true
    }
} // error
```

### class field

- js는 속성이 특성에 대한 값을 가지고 있음
- 속성의 특성을 볼수 있는 속성 설명자
- 속성의 특성을 변경/결정할 수 있는 속성 결정자
- 속성 설명자와 결정자를 가지고 데코레이터(함수)를 만듬

#### 속성 설명자

```js
const rabbit = {
  speed: 20,
  liveIn: 'ground'
};

console.log(Object.getOwnPropertyDescriptor(rabbit, 'speed'));
/*
{
  configurable: true,
  enumerable: true,
  value: 20,
  writable: true
}
*/
```

#### 속성 결정자?!
```js
Object.defineProperty(rabbit, 'speed', {
  writable: false,
  value: 20,
});

rabbit.speed= 30;
console.log(rabbit.speed); // 20
```

#### 데코레이터 만들기
```js
function readOnly(target, key, descriptor) {
  return {
    ...descriptor,
    writable: false,
  }; // return은descriptor이 될 값
}
```

```js
class Animal {
    constructor(){
        this.speed = 0
    }
}


class Rabbit extends Animal {
  @readOnly speed = 20;

  constructor(liveIn) {
    super();
    this.liveIn = liveIn;
  }
}
```

```js
class Turtle extends Animal {
  speed = 3;

  constructor(liveIn) {
    super();
    this.liveIn = liveIn;
  }

  @readOnly
  getSpeed() {
    return this.speed;
  }
}

const turtle  = new Turtle('sea');
console.log(turtle.getSpeed); // 3

turtle.getSpeed = () => 40
console.log(turtle.getSpeed); // 3

Turtle.prototype.getSpeed = () => 60
console.log(turtle.getSpeed); // 3
```

## 참고
- https://ui.toast.com/weekly-pick/ko_20200102
- https://steemit.com/tech/@ikanny/ecmascript-decorator
- https://wonism.github.io/what-is-decorator/
