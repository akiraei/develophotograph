---
layout: post
title: Programming Look-and-Say 07 (TWIL)
categories: [programming]
tags: [programming]
---

# CPS

- Continuation-passing-style은 함수의 결과를 콜백으로 전달받는 방식
- CPS를 통해서 제너레이터나 코루틴처럼 실행이 멈췃다가 계속되는 프로세스를 구현할 수 있음
    - 제네레이터나 코루틴이 없는 JAVA에서도 백만번 줄을 출력 가능함
- 코루틴이 resume되면서 실행되어야 하는 동작들이 컨티뉴에이션에 해당됨

## javascript
 - cps로도 코루틴과 같은 효과를 볼 수 있음
     - dispatch는 필요
     - 제네레이트의 next() 대신 컨티뉴에이션 콜백을 호출
 - 제네레이터 대비 불편한 점은 루프와 같은 제어프름을 직접 사용할 수 없음
     - 루프가 필요한 경우에는 지역 함수를 만들어 줘야함
