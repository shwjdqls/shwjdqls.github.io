---
layout: post
title: "[Flutter] 네이버, 카카오 Social Login 구현 (2. 네이버 로그인 구현)"
category: Flutter
tags: [Flutter, Social Login, Naver]
comments: true
---

이번에는 Flutter로 네이버 Social Login을 구현해 볼 것이다. 서버 설정을 하지 않았다면 아래 링크를 먼저 보고 오자.

[[Flutter] 네이버, 카카오 Social Login 구현 (1. 서버 설정)](https://shwjdqls.github.io/flutter-naver-kakao-social-login-1)

<br />

## 네이버 설정

[네이버 아이디로 로그인](https://developers.naver.com/products/login/api/api.md) 홈페이지로 가서 오픈 API 이용 신청을 한다.

<br />

![1]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-2/1.png)

원하는 애플리케이션 이름을 입력하고 제공 정보를 선택한다. 그리고 아래에서 환경 추가에서 Mobile 웹을 선택하면 새로운 입력 공간이 나온다.

<br />

![2]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-2/2.png)

여기서 서비스 URL에 REST API의 주소를 넣고, Callback URL에 "{REST API 주소}/naver/login", "{REST API 주소}/naver/token"을 추가한다.

내 경우에는

- 서비스 URL

"https://s80lt7nefi.execute-api.ap-northeast-2.amazonaws.com/api"

- Callback URL

"https://s80lt7nefi.execute-api.ap-northeast-2.amazonaws.com/api/naver/login"

"https://s80lt7nefi.execute-api.ap-northeast-2.amazonaws.com/api/naver/token"

이다.

<br />

## 서버 코딩

아래 내용을 해당 파일에 그대로 코딩한다. /naver/login 경로의 location에 있는 callback을 통해 앱으로 redirect 해준다.

##### index.js
```js
'use_strict';

const naver_auth = require('naver_auth');

exports.handler = async (event) => {
    switch (event.path) {
        case '/naver/login':
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
        
        case '/naver/token':
            if (event.httpMethod === 'GET') {
                const accessToken = event.queryStringParameters.accessToken;
                const result = await naver_auth.createFirebaseToken(accessToken);
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

네이버로부터 토큰을 발급받는 함수들을 만들어준다.

##### naver_auth.js
```js
'use strict';

const admin = require('./firebase_admin.js');
const axios = require('axios');

const URL = 'https://openapi.naver.com/v1/nid/me';

async function requestMe(accessToken) {
    const result = await axios.get(URL, {
        method: 'GET',
        headers: {'Authorization': 'Bearer ' + accessToken}
    });
    return result.data;
}

async function updateOrCreateUser(userId, email, displayName, photoURL) {
    const updateParams = {
        provider: 'NAVER',
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
    const body = me.response;
    const userId = `naver:${body.id}`;
    const userRecord = await updateOrCreateUser(userId, body.email, body.nickname, null);
    const updateParams = {
        uid: userRecord.id,
        email: body.email,
        provider: 'NAVER',
        displayName: '',
    };
    if (body.nickname) {
        updateParams['displayName'] = body.nickname;
    } else {
        updateParams['displayName'] = body.email;
    }
    if (body.profile_image) {
        updateParams['photoURL'] = body.profile_image;
    }
    return await admin.auth().createCustomToken(userId, {provider: 'NAVER'});
}

module.exports={
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

로그인 함수를 만든다. 로그인 클릭 시 아래 함수를 실행시키자. Client ID와 Client Secret 부분은 Naver Developers에서 본인 애플리케이션에 해당하는 값을 넣는다.

```dart
static const String BASE_URL = 'https://s80lt7nefi.execute-api.ap-northeast-2.amazonaws.com/api';

Future<UserCredential> loginWithNaver() async {
  final clientState = Uuid().v4();
  final authUri = Uri.https('nid.naver.com', '/oauth2.0/authorize', {
    'response_type': 'code',
    'client_id': {Client ID},
    'response_mode': 'form_post',
    'redirect_uri': '$BASE_URL/naver/login',
    'state': clientState,
  });
  final authResponse = await FlutterWebAuth.authenticate(
      url: authUri.toString(),
      callbackUrlScheme: "webauthcallback"
  );
  final code = Uri.parse(authResponse).queryParameters['code'];
  final tokenUri = Uri.https('nid.naver.com', '/oauth2.0/token', {
    'grant_type': 'authorization_code',
    'client_id': {Client ID},
    'client_secret': {Client Secret},
    'code': code,
    'state': clientState,
  });
  var tokenResult = await http.post(tokenUri);
  final accessToken = json.decode(tokenResult.body)['access_token'];
  final response = await http.get(
      Uri.parse('$BASE_URL/naver/token?accessToken=$accessToken')
  );
  return FirebaseAuth.instance.signInWithCustomToken(response.body);
}
```

<br />

로그인 후 UserCredential에서 원하는 데이터를 가져오면 된다. 아래는 이메일을 가져와서 보여주는 예시이다.

![3]({{ site.baseurl }}/images/flutter/naver-kakao-social-login-2/login.gif){: width="50%" height="50%"}{: .center-image}*이미 로그인이 되어 있어서 로그인 창은 뜨지 않음*

<br />

길고 긴 로그인이 끝났다! 다른 서비스 로그인에도 그대로 적용할 수 있으니 쉽게 재사용 할 수 있을 것이다. 다음은 카카오 로그인을 해보도록 하겠다.

<br />

[[Flutter] 네이버, 카카오 Social Login 구현 (3. 카카오 로그인 구현)](https://shwjdqls.github.io/flutter-naver-kakao-social-login-3)

<br />