---
layout: post
title: "[Android] CLEARTEXT communication to url not permitted by network security policy 오류 해결 방법"
category: Android
tags: [Android, Retrofit, Json]
comments: true
---

Retrofit을 사용하다 보면 이런 에러가 나오는 경우가 있다.

CLEARTEXT communication to url not permitted by network security policy

이는 url이 HTTPS가 아닌 HTTP로 통신을 했기 때문이다.

보안상 HTTPS 사용을 권장하지만 모든 url이 HTTPS만을 제공하는 것은 아니므로 어쩔 수 없이 사용해야 하는 경우에 해결하는 방법을 알아보자.

<br />

## 해결 방법

Android studio에서 res 우 클릭 -> New -> Android Resource Directory 클릭

![Directory2]({{ site.baseurl }}/images/android/error_network_security_policy/new_resource_directory.png){: .center-image}

Resource type에서 xml을 선택하고 OK를 눌러 생성한다.

res/xml에 XMLResource 파일을 추가하자

<br />

##### network_security_config

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">[처리할 url]</domain> // ex) jsonplaceholder.typicode.com
    </domain-config>
</network-security-config>
```

<br />

그리고 생성한 파일을 매니페스트의 application에 등록해주면 된다.

```
    <application
        ...
        android:networkSecurityConfig="@xml/network_security_config"
        ...
        >
```

<br />