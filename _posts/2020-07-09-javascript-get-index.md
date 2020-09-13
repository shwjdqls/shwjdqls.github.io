---
layout: post
title: "[Javascript] 인덱스 구하기"
category: Javascript
tags: [Javascript, Child, Index]
comments: true
---

Javascript를 쓰다 보면 특정 요소의 인덱스를 구하는 경우가 많다. 아래 인덱스를 구하기 위한 간단한 함수를 소개한다.

```javascript
const getIndex = element => {
	return Array.prototype.indexOf.call(element.parentNode.childNodes, element);
};
```

<br />

위 함수를 사용하여 요소를 넣으면 부모의 자식을 가져와서 그 중 자신의 인덱스를 확인할 수 있다.

<br />
