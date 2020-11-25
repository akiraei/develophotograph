---
layout: post
title: 루프 불변성 (TWIL)
categories: [programming]
tags: [programming]
---

# Loop Invariant

- 다른 말로는 `루프 증명`
- 루프가 '참'인지 증명하기 위한 조건
- 루프불변성에는 3가지 조건이 있다
    - 초기 조건
    - 유지 조건
    - 종료 조건

## 3 condition

### initialization
- 루프가 최초 반복을 하기전에 루프불변성이 '참'이어야 함

### maintenance
- 루프의 최초 반복 전에 루프불변성이 '참'라면, 루프불변성은 루프가 진행될 때에 계속 '참'을 유지해야함

### termination
- 루프가 종료될 때, 루프불변성이 구현하고자 하는 알고리즘의 타당성을 증명하는데 도움이 되는 유용한 특성을 가지고 있어야한다
- 즉, 루프불변성이 알고리즘 목적에 합당해야 한다

## example

### insertion sort

```js
const insertion_Sort = (nums) => {
  for (let i = 1; i < nums.length; i++) { // i는 루프변성
    let j = i - 1 // j는 루프변성.
    let temp = nums[i] // temp는 루프불변성 초기 j는 0이므로 initialization을 무조건 만족 (길이가 1인 배열)
    while (j >= 0 && nums[j] > temp) {
      nums[j + 1] = nums[j]
      j--
    }
    nums[j+1] = temp // for loop가 돌 때마다 temp는 항상 정렬됨. maintenance
  }
  return nums // nums가 정렬이 됨으로 루프 알고리즘이 목적에 합당. termination
}

```
