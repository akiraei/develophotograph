---
layout: post
title: divide-and-conquer
categories: [programming]
tags: [programming]
---

# Divide and Conquer
- 많은 알고리즘이 재귀 구조를 갖는다
- 문제 속의 부문제를 재귀적으로 해결한다
   - 분리
- 부문제들을 합쳐서 문제를 해결한다
   - 와 정복

## 3 step

### Divide
- 문제 속에서 부문제를 만든다
    - 부문제는 작은 인스턴스
    - 부문제는 서로 동일한 성격을 지님

### Conquer
- 부문제를 재귀적으로 해결한다

### Combine
- 부문제의 해결을 가지고 원문제를 해결한다


## example: Merge-sort

### Divide
- n-요소 순열을 2개의 n/2-요소 순열을 사용해서 정렬한다

### Conquer
- 2개의 순열은 merge-sort를 사용해서 재귀적으로 정렬한다

### Combine
- 2개의 정렬된 순열을 합병해서 원문제를 해결하는 정렬된 순열을 반환한다
