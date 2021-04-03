---
layout: post
title: "[Flutter] 네이버, 카카오 Social Login 구현 (1. 서버 설정)"
category: Flutter
tags: [Flutter, Social Login, Server, AWS, API Gateway, Lambda, VPC, Naver, Kakao]
comments: true
---

소셜 로그인은 간편하게 로그인 할 수 있는 수단으로써 널리 사용된다. Firebase를 이용하여 쉽게 구현할 수 있는데 Google, Facebook 등은 안내만 잘 따라하면 쉽게 구현할 수 있다.

하지만 국내에서 널리 사용되는 네이버와 카카오는 Firebase에서 지원하지 않는다. 이를 지원해주는 네이버의 [flutter_naver_login](https://pub.dev/packages/flutter_naver_login)와 카카오의 [kakao_flutter_sdk](https://pub.dev/packages/kakao_flutter_sdk) 두 라이브러리가 있긴 하다.

하지만 flutter_naver_login는 제대로 동작하지 않고(필자가 제대로 못했을 수도 있다.) kakao_flutter_sdk는 구현이 가능하나 최신 버전을 지원하지 않아 다른 라이브러리와 충돌이 잦아 불편하다.(2021년 4월 3일 기준)

어차피 REST API를 통해 네이버 로그인을 구현하면 카카오 로그인도 쉬우므로 둘 다 같은 방식으로 로그인을 구현해보자.

<br />

## Q. REST API? 뭔지도 잘 모르고 만들어 본적도 없어요.

REST API는 REST(Representational State Transfer) 아키텍처의 제약 조건을 준수하는 API(APplication Programming Interface)인데, 쉽게 말하면 데이터를 교환 가능하도록 하는 인터페이스이다. 아래 만드는 방법을 그대로 따라하면 어렵지 않게 만들 수 있다.

<br />

## Q. REST API가 상시 돌아가기 위해서는 서버가 필요한데 비용이 들지 않나요?

우리는 서버를 사용하지 않고 서버리스인 AWS의 API Gateway와 Lambda를 이용하여 REST API를 개발할 것이다.
프리티어에서 API Gateway와 Lambda는 매달 100만개의 요청을 무료로 지원한다.

참고: [API Gateway 요금](https://aws.amazon.com/ko/api-gateway/pricing/), [Lambda 요금](https://aws.amazon.com/ko/lambda/pricing/)

<br />

그러면 이제 본격적으로 개발을 시작해보자.

<br />

## Firebase 설정

먼저 [Firebase](https://console.firebase.google.com/u/0/?hl=ko)에서 프로젝트를 생성한다. 프로젝트 만드는 과정은 간단하므로 설명은 생략하겠다. 

<br />

![1]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/1.png)

만들고 나서 프로젝트 설정의 일반 탭에 들어가 앱 추가를 하여 Android와 iOS 모두 추가한다. 프로젝트 생성 시 지정한 패키지 이름을 써주면 된다.

그 후에 안내에 따라 google-services.json 파일을 android/app/src에, GoogleService-info.plist 파일을 ios/Runner에 넣어준다.(iOS의 경우 Xcode를 이용하여 넣지 않으면 실행 시 오류가 난다. Xcode가 없는 경우 다른 방법을 찾아 수동으로 넣어야 한다.)

<br />

![12]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/12.png)

서비스 계정에 들어가서 새 비공개 키를 생성한다. 이를 다운받아 저장해 놓는다.

<br />

## AWS API Gateway & Lambda 생성

[AWS](https://ap-northeast-2.console.aws.amazon.com/console/home?region=ap-northeast-2)에 접속하여 로그인을 한다. 계정이 없다면 회원가입을 한다. 회원가입을 하면 1년간 프리 티어로 이용할 수 있다.

위의 검색창에 API Gateway를 검색하여 API Gateway로 이동한다. API 생성을 클릭하여 API를 만들자.

<br />

![2]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/2.png)

REST API 구축을 클릭한다.

<br />

![3]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/3.png)

새 API를 선택한 후, 원하는 API 이름을 적어준다. 엔드포인트 유형은 지역으로 하고 API 생성을 클릭한다.

<br />

![4]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/4.png)

생성하면 새로운 창이 뜰 것이다. 작업에서 리소스 생성을 클릭하자.

<br />

![5]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/5.png)

여러 Lambda를 하나의 엔드포인트에서 처리하기 위해서 프록시를 구성할 것이다. 프록시 리소스로 구성을 체크하면 리소스 이름과 경로가 자동으로 생성된다. 프록시를 생성하면 리소스 탭에 새로 추가된다.

<br />

![6]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/6.png)

이제 Lambda를 생성해 API Gateway와 연결해보자. 상단에 Lambda를 검색하여 Lambda로 들어가 함수 생성을 클릭한다. 새로 작성을 클릭하고 함수 이름을 적는다. 런타임은 Node.js를 선택해주고 함수를 생성하자.

<br />

위에서 저장한 비공개 키 파일을 Lambda에 넣어주어야 한다. Lambda의 루트에서 파일을 생성 한 후 이름을 serviceAccountKey.json으로 한 뒤, 파일을 열어 내용을 복붙한다.

<br />

```js
var admin = require("firebase-admin");

var serviceAccount = require("./serviceAccountKey.json");

admin.initializeApp({
  credential: admin.credential.cert(serviceAccount)
});

module.exports = admin;
```

firebase_admin.js 파일을 생성하여 위 내용을 넣는다.

<br />

![7]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/7.png)

다시 API Gateway로 돌아와 리소스 탭에서 ANY를 클릭하고 Lambda 함수에 아까 생성한 함수 이름을 적어준다. 저장을 누르면 권한 부여한다는 창이 뜨는데 확인을 눌러주자.

<br />

![8]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/8.png)

API를 생성하고 함수를 연결했으니 제대로 연결되었는지 테스트해보자. 작업에서 API 배포를 클릭한다.

<br />

![9]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/9.png)

새 창이 뜨면 배포 스테이지를 새 스테이지, 스테이지 이름을 api로 적고 배포를 클릭한다.

<br />

![10]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/10.png)

여기까지 진행이 되면 REST API를 생성한 것이다! 상단에서 URL을 확인할 수 있다. 이 URL로 통신을 하면 Lambda 함수를 실행시킬 수 있다.

Lambda는 기본값으로 Hello from Lambda!를 리턴하도록 코딩되어있다. 이를 Postman으로 API를 테스트해보자. Postman이나 다른 API 테스트 도구가 없으면 [여기](https://www.postman.com/downloads/)에 접속하여 다운로드 받고 설치한다.

<br />

![11]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-1/11.png)

URL을 입력하고 Send를 눌러 응답을 확인한다. 여기에서 주의해야 할 점은 URL을 그대로 입력하지 말고 반드시 /를 붙여 하위에 내용을 입력해주어야 한다는 것이다. 그대로 입력하면 오류가 나올 것이다. 필자는 /test를 URL 뒤에 붙여 테스트하였고 응답을 확인하였다.

<br />

## AWS VPC 설정

Social Login을 구현하기 위해서는 Lambda에서 네이버나 카카오의 서버와 통신을 해야 하는데, 기본적으로 Lambda에서는 인터넷에 접속할 수 없어서 외부와 통신할 수 없다. 인터넷에 접속할 수 있도록 VPC 설정을 해주어야 한다.

여기까지 직접 설명하면 너무 길어지니 해당 공식 [문서 링크](https://aws.amazon.com/ko/premiumsupport/knowledge-center/internet-access-lambda-function/)를 올린다. 여기를 참고하여 진행해주길 바란다.

<br />

길었지만 Social Login을 구현할 수 있는 환경을 구축하였다. 사실 여기까지 오면 어려운 작업은 거의 다 마무리한 것이다. 다음 장에서 네이버와 카카오 로그인을 어떻게 연동하는지 알아보자.

<br />

[[Flutter] 네이버, 카카오 Social Login 구현 (2. 네이버 로그인 구현)](https://shwjdqls.github.io/flutter-naver-kakao-social-login-2)

[[Flutter] 네이버, 카카오 Social Login 구현 (3. 카카오 로그인 구현)](https://shwjdqls.github.io/flutter-naver-kakao-social-login-3)

<br />