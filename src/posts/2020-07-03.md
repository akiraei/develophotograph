---
layout: post
title: Programming Look-and-Say 03 (TWIL)
categories: [programming]
tags: [programming]
---

# 리스트 처리

- function next(x: [number]): number

## next()가 하는일

- 루프: 반복문
- 카운트: 같은 숫자의 반복 횟수를 셈
- 비교: 새로 읽은 숫자가 그 전 숫자와 같은 숫자인지 비교한다
- 문자열 생성: 카운트와 비교를 통해 얻은 결과를 바탕으로 문자열을 생성한다

얽혀있는 이 기능들을 분리하는 것이 목표

## 리스트 처리

예시
> [1,3,1,1,2,2,2,1]

### group
> [1,3,1,1,2,2,2,1] -> [[1], [3], [1,1], [2,2,2], [1]]

```js
function group (arr) {
    let result = []
    let pack = []
    let s = arr[0]
    for(let i = 0; i < arr.length; i++) {
        if(s === arr[i]){
          pack.push(arr[i])
        } else {
          result.push(pack)
          pack = [arr[i]]
        }
        s = arr[i]
    }
    result.push(pack)
    return result
}
```

### map
> [[1], [3], [1,1], [2,2,2], [1]] -> [[1,1], [1,3], [2,1], [3,2], [1,1]]

```js
function remake (arr) {
    const length = arr.length
    const number = arr[0]
    return [length, number]
}

const replaceMap = arr => arr.map(remake)
```

### concat
> [[1,1], [1,3], [2,1], [3,2], [1,1]] -> [1,1,1,3,2,1,3,2,1,1]

> Array.prototype.concat

## App-specfic vs. General

특정 애플리케이션에 속한 기능(함수)를 일반화된 기능(함수)로 분리하자

| App-specific | General  |
|--------------|----------|
| ant()        | group()  |
| next()       | map()    |
|              | concat() |

## 함수형 프로그래밍

- 함수형 자료구조
- 고차 함수

