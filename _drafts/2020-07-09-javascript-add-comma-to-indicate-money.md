---
layout: post
title: "[Javascript] 숫자에 콤마 찍어서 금액 표시하기"
category: Javascript
tags: [Javascript, Format, Comma, Money, Number]
comments: true
---


```javascript
const setMoneyTemplate = x => {
	return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",") + '원';
}
```



![app]({{ site.baseurl }}/images/android/get-distance-between-locations/map.png){: width="70%" height="70%" .center-image}*구글 지도: 749m*

![map]({{ site.baseurl }}/images/android/get-distance-between-locations/app.png){: width="50%" height="50%" .center-image}*계산 결과: 751m*

지도의 좌표를 그대로 찍어 확인해보았고, 거의 비슷한 것을 확인할 수 있다.

<br />
