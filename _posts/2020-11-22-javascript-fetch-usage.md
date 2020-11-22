---
layout: post
title: "[Javascript] Fetch 사용하여 API 요청하기"
category: Javascript
tags: [Javascript, Fetch, API]
comments: true
---

자바스크립트에서 API를 요청하고 응답을 받기 위해서는 어떻게 해야 할까? Fetch를 이용하여 쉽게 구현할 수 있다.
자바스크립트 파일을 커스텀하여 하나 만들어 사용하면 더욱 간단하다. 다음의 예시를 보자.

<br />

```javascript
// request.js
const baseURL = 'https://~' // 원하는 Base URL을 입력한다.
const request = async (action, method, data = null) => {
	if (data !== null) {
		data = JSON.stringify(data);
	}
	let response = await fetch(`${baseURL}${action}`, {
		method: method,
		headers: { 'test': 'value' }, // 원하는 해더를 입력한다.
		body: data
	});
	return await response.json();
}
```

통신할 URL을 상수로 미리 선언하자. async await 구문을 활용하여 보다 직관적으로 작성하였다.
앞에 if절에서 data가 있는 경우 JSON 형식으로 처리한다. 해더는 위와 같이 넣을 수 있으며 필요없으면 빼면 된다.
이는 예시로, 본인의 상황에 맞추어 커스텀하면 될 것이다.
이제 이를 어떻게 활용하는지 확인해보자

<br />

```javascript

...

let result = await request('user', 'GET');
if (result.code === 'SUCCESS') {
    // Do Something
}

...

```

이는 user의 정보를 요청하는 API의 예시이다. 이처럼 fetch를 모듈화하면 쉽게 사용할 수 있다.

<br />
