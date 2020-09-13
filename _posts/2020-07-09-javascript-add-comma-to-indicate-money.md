---
layout: post
title: "[Javascript] 숫자에 콤마 찍어서 금액 표시하기"
category: Javascript
tags: [Javascript, Format, Comma, Money, Number]
comments: true
---

화폐를 표시할 때 세 자리 마다 콤마를 찍어서 친절하게 표시해주어야 하는가? 그렇다면 다음 함수를 한 번 살펴보자.

```javascript
const converToWon = x => {
	return `${x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}원`;
}
```

<br />

정규식을 이용하여 한 줄로 간단하게 작성할 수 있다. 실제 사용 예제를 살펴보자.

```html
<div>
    <p></p>
    <p></p>
    <p></p>
</div>

<script>
    const converToWon = x => {
        return `${x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}원`;
    }
    const converToDollar = x => {
        return `$${x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",")}`;
    }

    let x = 1092012;
    document.querySelectorAll('p')[0].textContent = x; // 1092012
    document.querySelectorAll('p')[1].textContent = converToWon(x); // 1,092,012원
    document.querySelectorAll('p')[2].textContent = converToDollar(x); // $1,092,012
</script>
```

![example]({{ site.baseurl }}/images/javascript/add-comma-to-indicate-money/example.png){: width="20%" height="20%" .center-image}*예시*

위 함수를 응용하면, 달러나 다른 화폐로도 표시할 수 있다.

<br />
