---
layout: post
title: "[Flutter] 네이버, 카카오 Social Login 구현 (3. 카카오 로그인 구현)"
category: Flutter
tags: [Flutter, Social Login, Naver, Kakao]
comments: true
---

카카오 Social Login을 구현해 보자! 앞에서 네이버 로그인을 구현하는 방식과 동일하다. 서버 설정을 하지 않았거나 네이버 로그인이 궁금하다면 아래 링크를 참고.

[[Flutter] 네이버, 카카오 Social Login 구현 (1. 서버 설정)](https://shwjdqls.github.io/flutter-naver-kakao-social-login-1)

[[Flutter] 네이버, 카카오 Social Login 구현 (2. 네이버 로그인 구현)](https://shwjdqls.github.io/flutter-naver-kakao-social-login-2)

<br />

## 카카오 설정

[Kakao developers](https://developers.kakao.com/console/app) 홈페이지로 가서 로그인 후 애플리케이션을 추가한다.

<br />

![1]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/1.png)

앱 이름과 사업자명을 입력하고 저장을 클릭한다.

<br />

![2]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/2.png)

생성 후 플랫폼 설정하기를 클릭한다.

<br />

![3]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/3.png)

Web 플랫폼 등록을 클릭한다.

<br />

![4]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/4.png)

REST API 주소를 적어준다. api를 제외하고 적어야 한다.

<br />

![5]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/5.png)

도메인을 성공적으로 등록한 모습이다. 아래 등록하러 가기를 클릭하여 Redirect URI를 등록하러 가자

<br />

![6]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/6.png)

상태를 활성화하고 Redirect URI 등록을 클릭한다.

<br />

![7]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/7.png)

아까 입력한 REST API 주소에서 /api/kakao/login과 /api/kakao/token을 붙여서 넣어주면 끝!

<br />

![8]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/8.png)

추가적으로 동의 항목들을 원하는대로 설정해준다.

<br />

## 서버 코딩

아래 내용을 해당 파일에 그대로 코딩한다. /naver/login 경로의 location에 있는 callback을 통해 앱으로 redirect 해준다. 네이버 로그인과 같이 사용하는 경우 기존 index.js에서 case만 추가해주면 된다.

##### index.js
```js
'use_strict';

const kakao_auth = require('kakao_auth');

exports.handler = async (event) => {
    switch (event.path) {
        case '/kakao/login':
            if (event.httpMethod === 'GET') {
                const params = event.queryStringParameters;
                return {
                    statusCode: 307,
                    headers: {
                        Location: `callback://success?${new URLSearchParams(params)}`
                    },
                    body: JSON.stringify(params)
                };
            }
            break;
        
        case '/kakao/token':
            if (event.httpMethod === 'GET') {
                const accessToken = event.queryStringParameters.accessToken;
                const result = await kakao_auth.createFirebaseToken(accessToken);
                return {
                    statusCode: 200,
                    body: result
                };
            }
            break;
    }
};
```

<br />

카카오로부터 토큰을 발급받는 함수들을 만들어준다.

##### kakao_auth.js
```js
'use strict';

// https://github.com/FirebaseExtended/custom-auth-samples/tree/master/kakao

const admin = require('./firebase_admin.js');
const axios = require('axios');

const URL = 'https://kapi.kakao.com/v2/user/me';

async function requestMe(accessToken) {
    const result = await axios.get(URL, {
        method: 'GET',
        headers: {'Authorization': 'Bearer ' + accessToken}
    });
    return result.data;
}

async function updateOrCreateUser(userId, email, displayName, photoURL) {
    const updateParams = {
        provider: 'KAKAO',
        displayName: displayName,
    };
    if (displayName) {
        updateParams['displayName'] = displayName;
    } else {
        updateParams['displayName'] = email;
    }
    if (photoURL) {
        updateParams['photoURL'] = photoURL;
    }
    try {
        return await admin.auth().updateUser(userId, updateParams);
    } catch (e) {
        if (e.code === 'auth/user-not-found') {
            updateParams['uid'] = userId;
            if (email) {
                updateParams['email'] = email;
            }
            return await admin.auth().createUser(updateParams);    
        }
        throw (e);
    }
}

async function createFirebaseToken(accessToken) {
    const me = await requestMe(accessToken);
    const userId = `kakao:${me.id}`;
    let nickname = null;
    let profileImage = null;
    if (me.properties) {
        nickname = me.properties.nickname;
        profileImage = me.properties.profile_image;
    }
    const userRecord = await updateOrCreateUser(userId, me.kakao_account.email, nickname, profileImage);
    const updateParams = {
        uid: userRecord.id,
        email: me.kakao_account.email,
        provider: 'KAKAO',
        displayName: nickname,
    };
    if (nickname) {
        updateParams['displayName'] = nickname;
    } else {
        updateParams['displayName'] = me.kakao_account.email;
    }
    if (profileImage) {
        updateParams['photoURL'] = profileImage;
    }
    return await admin.auth().createCustomToken(userId, {provider: 'KAKAO'});
}

module.exports = {
    createFirebaseToken
};
```

<br />

## 앱 코딩

여기까지 서버 설정을 마무리하고 Flutter 앱으로 가보자. 먼저 필요한 dependency를 추가해준다. 버전은 최신 버전을 확인하여 넣어주자.

```yaml
dependencies:
  firebase_core: ^1.0.2
  firebase_auth: ^1.0.1
  uuid: ^3.0.2
  flutter_web_auth: ^0.2.4
  http: ^0.13.1
```

<br />

android/app/src/main/AndroidManifest.xml로 가서 아래 코드를 추가한다. android:scheme의 callback은 서버에서 redirect에 사용하는 값으로 동일하게 해주면 된다.

```xml
<activity android:name="com.linusu.flutter_web_auth.CallbackActivity" >
    <intent-filter android:label="flutter_web_auth">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="callback" />
    </intent-filter>
</activity>
```

<br />

로그인 함수를 만든다. 로그인 클릭 시 아래 함수를 실행시키자. REST_API_KEY에 카카오에서 발급받은 키를 넣는다.

```dart
static const String BASE_URL = 'https://s80lt7nefi.execute-api.ap-northeast-2.amazonaws.com/api';

Future<UserCredential> loginWithKakao() async {
  final clientState = Uuid().v4();
  final authUri = Uri.https('kauth.kakao.com', '/oauth/authorize', {
    'response_type': 'code',
    'client_id': {REST_API_KEY},
    'response_mode': 'form_post',
    'redirect_uri': '${Server.BASE_URL}/kakao/login',
    'scope': 'account_email profile',
    'state': clientState,
  });
  final authResponse = await FlutterWebAuth.authenticate(
      url: authUri.toString(),
      callbackUrlScheme: "webauthcallback"
  );
  final code = Uri.parse(authResponse).queryParameters['code'];
  final tokenUri = Uri.https('kauth.kakao.com', '/oauth/token', {
    'grant_type': 'authorization_code',
    'client_id': {REST_API_KEY},
    'redirect_uri': '${Server.BASE_URL}/kakao/login',
    'code': code,
  });
  final tokenResult = await http.post(tokenUri);
  final accessToken = json.decode(tokenResult.body)['access_token'];
  final response = await http.get(
      Uri.parse('${Server.BASE_URL}/kakao/token?accessToken=$accessToken')
  );
  return await FirebaseAuth.instance.signInWithCustomToken(response.body);
}
```

<br />

로그인 후 UserCredential에서 원하는 데이터를 가져오면 된다. 아래는 이메일을 가져와서 보여주는 예시이다.

![3]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-3/login.gif){: width="50%" height="50%"}{: .center-image}*이미 로그인이 되어 있어서 로그인 창은 뜨지 않음*

<br />

이렇게 네이버와 카카오 로그인 모두 완료하였다. 이 방법으로 다른 웹 플랫폼을 지원하는 소셜 로그인을 사용할 수 있으니 알아두면 도움이 될 것이다.

<br />