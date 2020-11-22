---
layout: post
title: "[Node.js] Error: Invalid login: 535-5.7.8 Username and Password not accepted 에러 해결"
category: Node.js
tags: [Node.js, nodemailer, error]
comments: true
---

Node.js에서 nodemailer를 활용하여 메일을 보낼 수 있다. 이 때 아래와 같은 오류가 나올때가 있다.

<br />

Error: Invalid login: 535-5.7.8 Username and Password not accepted. Learn more at 535 5.7.8 https://support.google.com/mail/?p=BadCredentials e27sm18859017pfj.129 - gsmtp

<br />

해결 방법을 구글링해보면 메일 계정의 '보안 수준이 낮은 앱 엑세스 허용'을 해야한다고 나온다. 하지만 해도 그대로이다.

이때는 이 [링크](https://myaccount.google.com/security?pli=1)에 들어가서 2단계 인증을 하고, 앱 비밀번호를 설정해주자. 이 앱 비밀번호를 nodemailer의 비밀번호로 넣으면 정상 작동한다.

<br />

![example]({{ site.baseurl }}/images/nodejs/nodejs-nodemailer-error/image.png){: .center-image}*앱 비밀번호 설정*

<br />
