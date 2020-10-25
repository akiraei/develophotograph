---
layout: post
title: Programming Look-and-Say 06 (TWIL)
categories: [programming]
tags: [programming]
---

# Coroutine

- 비선점 멀티태스킹을 위해 서브루틴을 일반화한 컴퓨터 프로그램의 구성요소
- 실행을 중단하고 재시작할 수 있는 진입점을 여러 개 가질 수 있다
- ex) 협력적 멀티미디어, 예외, 이벤드 루프, 이터레이터, 무한 리스트, 파이프
- 일반 함수
  - 함수 호출시 함수로 점프하면서 콜스택이 쌓임
  - 호출된 함수가 종료되면 원래 호출했던 위치로 되돌아 온다
- 코루틴
  - 코루틴A의 a0 ---호출---> 코루틴B
  - B의 b0 ---호출(혹은 resume)---> A
  - A의 a1 ---> B
  - B의 b1 ---> A
  - ... 상위 루틴을 알 수 없게 됨

## Go의 고루틴과 채널

- 코루틴과 비슷한 역할을 하는 go의 루틴
- 순차 처리 개념
  - 쓰레드처럼 동작
  - 여러 고루틴은 자체 스케줄러를 통해 더 적은 갯수의 쓰레드에서 실행됨
  - 채널을 매개로 동기화, 데이터 교환을 함
  - 스케줄러를 통해서 채널의 접근을 블록/허가
  - 채널을 통해서 코루틴처럼 제어권을 주고받음
- 제네레이터
  - 읽기 채널을 반환하면서, 내부에서 따로 고루틴을 만들어 그 채널로 값을 보내는 함수
- 개미수열
  - n번 줄을 만들기 위해 ant() 함수는 첫줄을 생성
  - 채널을 next()로 n번 감싸줌
    - n개의 고루틴이 시작됨
    - 채널로 연동됨
  - 채널을 통해 모두 엮이면서 마지막 채널(n번 줄)에서 값을 읽어가는 만큼만 계산됨
    - 지연 리스트의 효과도 그대로 얻음

## 고루틴과 코루틴

### 공통점

||코루틴|고루틴|
||:-----|:-----|
||채널로 동기화|협력적으로 실행됨(컨텍스트 스위칭)|
||협력적 멀티태스킹, yield/resume으로 제어 흐름을 주고 받음|채널을 통해 동기적으로 값을 주고 받음|
||쓰레드 하나에서 동작|고루틴 갯수보다 적은 스레드에서 멀티플렉싱이 실행됨|

### 고루틴을 흉내낸다

- 단일 쓰레드에 멀티플렉싱되는 순차 프로세스를 표현
- 채널을 이용해서 프로세스 간 데이터 교환을 동기화
- 결론: 프로세스들의 실행을 담당하는 스케줄러를 구현해야 함

### 코루틴을 흉내낸다

- 멈췄다가 재시작 가능한 프로세스를 구현
- 프로세스를 실행시켜 줄 디스패쳐를 만든다
  - 디스패처는 한 번에 하나의 프로세스를 실행하면서 하나가 멈추면 적절히 다음 프로세스를 선택/실행

## 코루틴과 개미수열

1. n번 코루틴은 n-1이 출력할 입력을 읽기위해 yield
1. 디스패처는 n-1번 코루틴을 resume
1. n-1번 코루틴은 출력과 함께 yield
1. 디스패처는 n번 코루틴을 resume

## 제네레이터 코루틴

- 제네레이터는 중단하면 호출한 위치로만 되돌아가는 반쪽 코루틴
- 디스패치 함수에서 제네레이터를 선택하여 실행하면 제너레이터를 코루틴처럼 이용 가능
  - 다수의 코루틴으로 협력적 동시성을 구현

```js
const READ = { type: "read" };
// yield의 이유가 read. ex: n-1의 출력을 읽기 위해
const WRITE = value => ({ type: "write", value });
// yield의 이유가 write (값을 출력하기 위해)

function* gen() {
  // gen()는 한 프로세스를 담당이자 그 자체

  let prev = yield READ;
  // gen(제네레이터)가 next(a)를 하면 a의 값이 prev가 된다
  // 즉, gen(제네레이터)는 READ를 출력하면서(dispatcher에 READ를 요청함)
  // 동시에 a의 값을 취득하여 prev에 대입한다
  // 여기서부터 코드가 재시작됨

  let count = 1;
  // 일단 prev에 대해서 갯수가 한개 이상은 될테니 count는 1에서 시작
  let value;
  while ((value = yield READ)) {
    // [할당 연산자가 return 하는 것](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Assignment)
    // 루프 시작시에 READ(요청)를 yield하고 이를 통해 값을 받는다. 받은 값은 value에 대입. 표현식은 value를 return --- <3>
    // READ를 통해서 값을 요청하고 요청한 값이 undefined가 아닐 때까지 루프를 반복 --- <5>

    if (prev === value) {
      count++;
      // prev가 value와 같으면 count를 늘린다
    } else {
      // prev가 value와 같지 않다면
      // 즉 새로운 prev가 등장하나면
      yield WRITE(count);
      // 갯수를 출력하고
      yield WRITE(prev);
      // prev를 출력한다
      prev = value;
      // 그리고 새로운 value를 prev에 대입한다
      count = 1;
      // count도 1로 초기화
    }
  }
  // 마지막 남은 카운트와 prev
  yield WRITE(count);
  // 갯수를 출력하고
  yield WRITE(prev);
  // prev를 출력한다
}
```

- 제너레이터는 이터레이터로 사용됨
- 코루틴으로 사용되기 위해서는 '코루틴화된 제너레이터'를 동작시켜주는 디스패쳐가 필요함
  - 디스패쳐 함수는 코루틴들을 실행하면서 코루틴의 yield하는 연산을 처리
  - read와 write를 구분함; 타입을 구분하는 이유는 결국 코루틴이기 때문에
      - 코루틴은 호출 되는 방향을 직접 지정할 수 있어야 한다
      - 타입이 없으면 제네레이터의 호출 방향이 일방적으로 되돌아 오기 때문
      - 이 경우와 같이 타입이 2개면 줄 단계?!가 낮아지는 방향과 높아지는 방향 둘로 지정이 가능하다

```js
function* dispatch(processes) {
  let value;
  // 개미수열의 숫자 값이 순차적으로 지나가는... 작은 메모리 같은 역할?!
  const n = processes.length - 1;
  // 원문에 n을 declare하는 부분이 없어서 설정함
  let cursor = n;
  // n번줄에 cursor를 세팅함

  while (true) {
    let readOrWrite = processes[cursor].next(value);
    // processes[cursor]는 gen(제네레이터)의 인스턴스
    // 특정 프로세스(코루틴)에게 값 value를 전달

    switch (true) {
      case !readOrWrite.done && readOrWrite.value.type === "read": {
        // readOrWrite.done이 false
        // readOrWrite가 read 타입이면
        // 전 순서의 process를 향하도록 cursor를 옮김
        cursor--;
        // 즉, 전 순서의 process(gen의 인스턴스)에게 값을 받아 오는 요청
        // n에서 시작한 커서는 n = 0 일 때까지 감소하고 <1>의 process를 호출한다
        break;
      }

      case !readOrWrite.done &&
        readOrWrite.value.type === "write" &&
        cursor !== n: {
        // readOrWrite가 write 타입이면서 cursor가 n과다르면
        value = readOrWrite.value.value;
        // value에 `WRITE의 인스턴스의 value`를 대입
        cursor++;
        // value에 값을 지정했기에 임무를 완수하고 READ를 요구했던 process인 다음으로 커서를 옮긴다
        // 다음 process로 넘어가면서 <3>을 만날 수 있다. 그러면 READ 요구가 다시 들어오고 case를 다시 만나게 될 것이다.
        // <1>은 WRITE이다. 여기서부터 case가 실행된다.
        // value의 값은 커서가 지정한 gen().value(type WRITE 객체)의 value(제네레이터 gen 인스턴스)의 로 갱신됨. 그 값은 'count'가 될 수도 있고 'prev'가 될 수도 있음 --- <2>
        break;
      }

      case readOrWrite.done && cursor !== n: {
        // <5>처럼 결국 값의 출력이 종료되면 done:true가 된다. ('한 줄'에 해당하는 모든 수가 출력됨)
        // 커서가 n번 gen()를 가르키는 것이 아니면
        value = undefined;
        cursor++;
        // 다음으로 넘어가기 전에 value는 undefined가 되고(value를 초기화하고) 커서는 다음 gen()를 가르킨다
        break;
      }

      case !readOrWrite.done &&
        readOrWrite.value.type === "write" &&
        cursor === n: {
        // readOrWrite가 write 타입이면서 cursor가 n과 동일하면
        yield readOrWrite.value.value;
        // readOrWrite.value는 WRITE의 인스턴스
        // 결론: WRITE의 인스턴스의 value를 출력한다
        // <2>로 인해서 value는 갱신되고 커서 값이 n이 되면서 value를 yield 함
        // ant(n)은 n의 숫자를 출력하는 것. 즉 cursor가 n일 때만 yield가 작동하면 된다.
        // (n에 해당하는 process의 값이 원하는 값이 었음)
        break;
      }

      case readOrWrite.done && cursor === n: {
        return;
        // gen()가 출력한 값이 done이면서 커서가 n번 gen()을 가르키면 리턴!; 종료
      }
      default: {
        break;
      }
    }
  }
}
```

```js
function* ant(n) {
  let processes = new Array(n + 1);
  processes[0] = (function*() {
    yield WRITE(1);
    // 초기에 1을 작성하는 (yield, 즉 출력하는) 제네레이터. seed. --- <1>
  })();
  for (let i = 1; i < n + 1; i++) {
    // processes 배열에는 [0]에 제네레이터 한 개가 있고,
    processes[i] = gen();
    // processes라는 배열에 gen() 제네레이터를 n개 가득 채움, 총 n+1개
  }
  yield* dispatch(processes); //--- <4>
  // yield가 아니라 yield*가 중요함
}
```

## js-csp
 - 고루틴처럼 의존 관계를 채널로 규명한다
     -  yield에서 채널 객체와 채널 연산(read/write)를 지정
     -  dispatch()에서 해당 연산에 따라 다음 실행될 코루틴을 선택
 - js-csp는 고루틴을 구현함

## 개미 수열 복잡도
 - n번 줄의 m번째 글자를 계산하기 위한 복잡도를 따져보자

### 공간 복잡도
 - n개의 코루틴이 필요
 - 각 코루틴은 고정된 크기의 메모리만 사용
     - 고정된 메모리: value, count
 - 결론: O(n)

### 시간 복잡도
 - n번 줄의 m번째 글자를 계산하기
      - n-1번 줄의 m번째 보다 적은 숫자의 글자를 계산해도 된다
 - m 부터 30%씩 줄어드는 등비 수열의 합만큼 계산해야 함
      - m * ( 1 - 0.7^n) / (1 - 0.7)
 - 줄어드는 등비수열의 합은 초항의 크기에 비례함; O(m) --- <1>
 - .... 결론이 O(n+m)이라는데 나는 왜 이해가 안 갈까...
      - <1>까지는 이해가 되는데
