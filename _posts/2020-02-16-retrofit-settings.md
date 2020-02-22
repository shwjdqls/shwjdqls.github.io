---
layout: post
title: "[Android] Retrofit 사용하기"
tags: [Android, Retrofit]
comments: true
---

# Retrofit 이란?
Retrofit은 REST API로, 서버와 클라이언트간 Http 통신을 위한 인터페이스를 뜻합니다.
(여기서는 REST에  대한 자세한 내용은 생략하도록 하겠습니다.)
쉽게 말해, 클라이언트에서 서버로 어떠한 요청을 보내면 서버는 그 요청에 대한 응답을 클라이언트로 보내주게 되는데, 이 일련의 과정들을 쉽게 사용 할 수 있도록 도와주는 역할을 하는 것이 바로 Retrofit 입니다.

링크
http://square.github.io/retrofit

# gradle 설정

app gradle에 추가
```
// Retrofit2
implementation 'com.squareup.retrofit2:retrofit:2.5.0'
```

# 권한 설정

Manifest에 추가
```xml
<uses-permission android:name="android.permission.INTERNET" />
```
위험하지 않은 권한이므로 따로 코드 X

# 통신 데이터

테스트 여기서
http://jsonplaceholder.typicode.com/

# retrofit 초기화
