---
layout: post
title: insertion-sort (TWIL)
categories: [programming]
tags: [programming]
---

# Insertion Sort

## 삽입정렬

- `key`값이 존재. 배열의 어떤 요소를 비교할 지 결정
- `key`에 해당하는 요소를 이전 순서에 해당하는 모든 숫자와 순차대로 비교해서 위치를 잡음
    - `key`값은 2번째 부터 시작
    - 전체배열이 존재하고 비교할 때마다 작은 부분배열이 생성됨. 2개의 배열!

```js
const insertion_Sort = (nums) => {
  for (let i = 1; i < nums.length; i++) { // --- <1>
    let j = i - 1
    let temp = nums[i]
    while (j >= 0 && nums[j] > temp) { // --- <2>
      nums[j + 1] = nums[j]
      j--
    }
    nums[j+1] = temp
  }
  return nums
}

```

## big-O

### 최악의 경우

- <1>이 n번 발생
- <2>가 n번 발생
- 결론: O(n^2)
