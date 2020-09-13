---
layout: post
title: "[Javascript] 소수점 특정 자리수까지 0으로 채워서 표시하기"
category: Javascript
tags: [Javascript, RoundOff, Point, Float]
comments: true
---

소수를 소수점 이하 특정 자리수까지 0으로 채워서 표시하고 싶을때는 어떻게 할까? Javascript에서는 float형에서 toFixed라는 함수를 제공한다. 다음은 소수점 둘 째자리까지 반올림하고 특정 변수까지 0으로 표시하는 함수이다.

```javascript
const getFloatFixed = (value, fixed) => {
	return parseFloat(Math.round(value * 100) / 100).toFixed(fixed);
};
```

<br />

Math.round() 함수를 사용하여 반올림을 해주고 그 후에 toFixed() 함수로 자리수를 표시한다. 실제 사용 예제를 살펴보자.

```html
<div>
    <p></p>
    <p></p>
    <p></p>
</div>

<script>
    const getFloatFixed = (value, fixed) => {
        return parseFloat(Math.round(value * 100) / 100).toFixed(fixed);
    };

    let x = 1.987654;
    document.querySelectorAll('p')[0].textContent = x; // 1.987654
    document.querySelectorAll('p')[1].textContent = getFloatFixed(x, 2); // 1.99
    document.querySelectorAll('p')[2].textContent = getFloatFixed(x, 4); // 1.9900
</script>
```

![example]({{ site.baseurl }}/images/javascript/show-float-number/example.png){: width="20%" height="20%" .center-image}*예시*

특정 자리수까지 표시가 필요할 때 toFixed() 함수를 기억하자!

<br />
