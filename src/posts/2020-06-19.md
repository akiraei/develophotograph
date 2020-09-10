---
layout: post
title: Programming Look-and-Say 01 (TWIL)
categories: [programming]
tags: [programming]
---

# Look and Say

## Look and Say

> 1
> 11
> 21
> 1211
> 111221

## for and string

```js

// convert from Java

class LookAndSay {
    constructor () {
        this.s = '1'
        for (let line = 0; line < 10; line++) {
            let length = 1;
            let head = s[0]
            let result = ''
            for (let i = 1; i < s.length ; i++){
                if(s.[i] = head){
                    length ++
                } else {
                    result += length;
                    result += head;
                    length = 1;
                    head = s[i];
                }
                }
                result += length;
                result += head;
                s = result
            }
            console.log(s)
    }
}
```

## list

```js
const sequence = () => {
  something;
};
const iterate = () => {
  something;
};
const ant = group => interate([1], sequence(length, head));
console.log(ant(10));
```

- `for, staring`과 매우 다른 동작

## 기존 `for, string`에서 ant()와 next() 분리

```js
// convert from Java

class LookAndSay {
  constructor() {}

  ant(n) {
    let s = "1";
    for (let line = 0; line < n; line++) {
      s = next(s);
    }
    return s;
  }

  next(s) {
    let length = 1;
    let head = s[0];
    let result = "";
    for (let i = 1; i < s.length; i++) {
      if (s[i] === head) {
        length++;
      } else {
        result += length;
        result += head;
        length = 1;
        head = s[i];
      }
    }
    result += length;
    result += head;
    return result;
  }
}
```

## Wishful Thinking

어떠한 코드/함수가 이미 있다고 생각하고 개발을 진행하는 것
마치 ant() 함수가 미리 있다고 생각하는 것
```js
ant(10) // 10번째 줄 출력
```

전체적인 개발의 맥을 잡는데 벗어나지 않고 집중할 수 있도록 도와주는 방법
