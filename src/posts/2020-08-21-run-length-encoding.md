---
layout: post
title: Run-Length Encoding
categories: [programming]
tags: [programming]
---

# Run-Length Encoding

- 연속된 동일한 글자를 횟수로 표기함
- 연속된 글자가 없다면 효율이 매우 떨어짐

```js
const counter = (before, now, count) =>
  before === now ? { key: 'add', value: count + 1 } : { key: 'change', value: now };

const reducer = (string) => {
  let result = '';
  let count = 1;
  for (let i = 0; i < string.length; i++) {
    if (i === 0) {
      result = result + string[i];
    } else {
      const object = counter(string[i - 1], string[i], count);
      if (object.key === 'add') count = object.value;
      if (object.key === 'change') {
        re = re + String(count) + object.value;
        count = 1;
      }
    }
  }
  result = result + String(count);
  return result;
};
```

```js
const string = 'aaaababbbbaaacccabacaaacaaa';
reducer(string) // "a4b1a1b4a3c3a1b1a1c1a3c1a3"
```
