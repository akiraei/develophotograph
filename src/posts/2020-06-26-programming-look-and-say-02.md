---
layout: post
title: Programming Look-and-Say 02 (TWIL)
categories: [programming]
tags: [programming]
---

# regexp

- Advice of Rob Pike
> 파서나 렉서를 만들 때 처음부터 ㅊ정규표현식을 사용하기보다는 표준적인 기법을 사용해서 직접 텍스트를 처리하라.
> 빠르고, 깔끔하고, 유지ㅎ보수 하기 쉽다


## 반복 문자열 찾기
```js
function ant(n) {
    let s = '1';
    for (let i = 0; i < n; i++) {
        s = next(s)
    }
    return s;
}

function next(s) {
    return s.replace(/(.)\1*/g, (g, c) => g.length + c);
}

console.log(ant(10))
```

## App-specfic vs. General

특정 애플리케이션에 속한 기능(함수)를 일반화된 기능(함수)로 분리하자

| App-specific | General   |
|--------------|-----------|
| ant()        | replace() |
| next()       |           |
