---
layout: post
title: vuex - 넌 누구니? 
tags: [frontend]
---


###### 아래의 내용은 vuex의 공식 문서를 제 깜냥대로 번역한 것입니다
___
<br>
<br>

vuex는 vue를 위한 상태관리 패턴 + 라이브러리이다. 
어플리케이션의 모든 컴포넌트에 중앙화된 저장소와 같이 데이터를 제공한다.

1. 예상 가능한 방향으로 뮤테이션 되도록 정해져 있다.
2. vue dev extension과 결합된다. 이는 zero-config time-travel debugging 
과 state snapshot export/import 등의 향상된 기능을 제공한다.

 
## 상태 관리 패턴이 무엇일까?

```js
new Vue({
  // state
  data () {
    return {
      count: 0
    }
  },
  // view
  template: `
    <div>{{ count }}</div>
  `,
  // actions
  methods: {
    increment () {
      this.count++
    }
  }
})
```
위는 다음의 부분으로 나뉘어진 자기폐쇄형 앱이다.

1. 상태: 앱을 움직이는 source of truth
2. 뷰: 상태를 표현
3. 액션: 뷰에 따른 유저의 input에 의한 반작용으로 상태를 바꿀 수 있는 방법

그러나 단순성은 공통의 상태를 공유하는 복수의 컴포넌트에 의해 쉽게 깨지게 된다.

1. 복수의 뷰는 같은 상태의 조각에 의존될 수 있다.
   > 넘겨지는 props는 깊숙히 자리한 컴포넌트에게 지루할/반복적일 수 있다. 
    그리고 형제 컴포넌트들에 작동이 간단하게 되지 않을 수 있다. 
2. 다른 뷰로 부터 시작된 액션은 같은 상태의 조각을 뮤테이션 할 수 있다.
   > 직접적인 부모 / 자식 인스턴스 참조에 도달하거나 이벤트를 통해 상태의 여러 복사본을
       변경 및 동기화하려고하는 등의 솔루션을 사용하고 있습니다.

이 두 가지 패턴은 모두 부서지기 쉽고 유지 보수가 불가능한 코드로 빠르게 이어집니다.

그렇다면 구성 요소에서 공유 상태를 추출하고 이를 글로벌 싱글 톤에서 관리하지 않는 이유는 무엇입니까?
  - 이를 통해 우리의 구성 요소 트리는 커다란 "뷰"가되고 모든 구성 요소는 트리에 
상관없이 상태에 액세스하거나 동작을 트리거 할 수 있습니다!
또한 상태 관리 및 특정 규칙 적용과 관련된 
개념을 정의하고 분리함으로써 코드의 구조와 유지 관리 기능을 향상시킵니다.
Flux, Redux 및 The Elm Architecture에서 영감을 얻은 Vuex의 기본 아이디어입니다. 
다른 패턴과 달리 Vuex는 Vue.js가 효율적인 업데이트를 위해 세분화 
된 반응 시스템을 활용하도록 특별히 고안된 라이브러리 구현입니다.


## 언제 써야 하나?

 Vuex가 공유 된 상태 관리를 처리하는 데 도움을 주지만, 
 Vuex는 더 많은 개념과 boilerplate 비용도 함께 부담합니다. 
 그것은 단기간 및 장기간의 생산성 간의 균형입니다.

대규모 SPA를 구축하지 않고 바로 Vuex에 뛰어 들었다면, 
장황하고 힘든 일일 수 있습니다. 앱이 단순하다면 정상적으로 작동합니다. 
 Vuex 없이도 괜찮을 것입니다. 간단한 스토어 패턴만이 필요할 수도 있습니다. 
 그러나 중대형 규모의 SPA를 구축하는 경우, 
 Vue 구성 요소 외부의 상태를 보다 잘 처리 할 수있는 방법을 고민하게 될 것입니다.
  이 때, Vuex는 자연스럽게 다음 단계가 될 것입니다.



