---
layout: post
title: "[Android] Retrofit 사용법 및 Json 파싱하기"
tags: [Android, Retrofit, Json]
comments: true
---

## Retrofit 이란?
Retrofit은 REST API로, 서버와 클라이언트간 Http 통신을 위한 인터페이스를 뜻한다.

서버와 클라이언트간 통신에는 여러 고려해야 할 사항들이 많은데 retrofit은 이를 쉽게 관리해주는 라이브러리로 정말 널리 쓰인다. 

보통 okhttp와 함께 사용하는데 먼저 retrofit만 사용하여 프로젝트에 한 번 적용해보자.

자세한 내용은 [공식 문서](http://square.github.io/retrofit)를 참고하면 된다.

## gradle 설정
```
dependencies {
    ...
    // Retrofit2
    implementation 'com.squareup.retrofit2:retrofit:2.5.0'
    // Json Parser
    implementation 'com.squareup.retrofit2:converter-gson:2.4.0'
    ...
}
```

app gradle에 retrofit을 추가한다. json 형식으로 응답하는 경우 gson도 같이 추가한다. 다른 형식이라면 그에 맞는 parser를 사용해야 한다.

## 권한 설정

```xml
<manifest

    <uses-permission android:name="android.permission.INTERNET" />
    ...
>
```

서버와 통신하기 위해 Manifest에 인터넷 권한을 추가한다. 사용자에게 따로 권한을 요청할 필요는 없다.

## POJO 생성

응답 데이터의 형식을 지정해주는 클래스가 필요하다. 이를 POJO(Plain Old Java Object)라고 부른다.

REST API 테스트는 [JsonPlaceHolder](http://jsonplaceholder.typicode.com/) 사이트를 이용하였다.

```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
  "body": "quia et suscipit\nsuscipit recusandae consequuntur expedita et cum\nreprehenderit molestiae ut ut quas totam\nnostrum rerum est autem sunt rem eveniet architecto"
}
```

위 json 파일은 우리가 테스트 할 url인 http://jsonplaceholder.typicode.com/posts/1 에서 가져온 값이다.

이를 바탕으로 POJO 클래스를 생성해보자.

```java
data class Posts(
    var userId: Int,
    var id: Int,
    var title: String,
    var body: String
)
```

코틀린의 데이터 클래스를 사용하여 작성하였는데 자바의 경우는 변수를 선언하고 getter와 setter가 따로 존재해야 한다. 

직접 만들기가 어려우면 [여기](http://pojo.sodhanalibrary.com/)에서 json 파일만 넣어주면 자바로 POJO 클래스를 만들어준다.

## retrofit 생성 및 요청

먼저 baseUrl과 컨버터를 적어 빌드한다.

```java
val retrofit = Retrofit.Builder()
            .baseUrl("http://jsonplaceholder.typicode.com/")
            .addConverterFactory(GsonConverterFactory.create())
            .build()
```

그리고 아래와 같이 요청하면 콜백으로 응답을 받을 수 있다. 콜백 함수의 안에는 자유롭게 구현하면 된다.

```java
val service = retrofit.create(RetrofitAPI::class.java)
service.getPosts().enqueue(object: Callback<Posts> {
    override fun onFailure(call: Call<Posts>, t: Throwable) {
        // 요청에 실패할 경우
        t.message?.let {
            Toast.makeText(this@MainActivity, it, Toast.LENGTH_SHORT).show()
        } ?: Toast.makeText(this@MainActivity, "Unknown error", Toast.LENGTH_SHORT).show()

    }

    override fun onResponse(call: Call<Posts>, response: Response<Posts>) {
        // 요청에 성공한 경우
        response.body()?.let {
            val userId = it.userId
            val id = it.id
            val title = it.title
            val body = it.body
            textView.text = "userId: $userId\nid: $id\ntitle: $title\nbody: $body"
        } ?: Toast.makeText(this@MainActivity, "Body is null", Toast.LENGTH_SHORT).show()
    }
})
```

<br />

![Directory2]({{ site.baseurl }}/images/android/retrofit_usage_json/result.png){: width="50%" height="50%" .center-image}*요청 결과*

<br />