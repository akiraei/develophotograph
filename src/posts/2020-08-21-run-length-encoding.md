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
const encode = (string) => {
  // 알파벳만 존재하는 문자열만 가능
  let result = '';
  let count = 1;
  let character = '';
  for (let i = 0; i < string.length; i++) {
    if (i === 0) {
      character = string[i];
    } else {
      if (string[i] === character) {
        count++;
      } else {
        result = result + String(count) + character;
        character = string[i];
        count = 1;
      }
    }
  }
  result = result + String(count) + character;
  return result;
};

const string = 'aaaababbbbaaacccabacaaacaaa';
const encoded = encode(string); // "4a1b1a4b3a3c1a1b1a1c3a1c3a"

const decode = (string) => {
  const array = string.split(/([a-zA-Z])/).filter((e) => Boolean(e));
  if (array.length % 2 === 1) throw 'Error';
  let result = '';
  for (let i = 0; i < array.length / 2; i++) {
    for (let j = 0; j < Number(array[2 * i]); j++) {
      result = result + array[2 * i + 1];
    }
  }
  return result;
};

const decoded = decode(encoded);

console.log(decoded === string); //true
``
