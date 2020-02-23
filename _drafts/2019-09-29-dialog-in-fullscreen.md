---
layout: post
title: "[Android] 풀스크린으로 다이얼로그 띄우기"
category: Android
tags: [다이얼로그, 풀스크린]
comments: true
---

Dialog는 사용자에게 결정을 내리거나 추가 정보를 입력하라는 내용을 표시하는 작은 창입니다. Dialog는 화면을 가득 채우지 않으며 사용자의 입력을 기다립니다.

Dialog를 사용하기 위해 Dialog의 서브 클래스인 AlertDialog를 사용하게 됩니다. 아래와 같이 빌더 패턴을 이용하여 Dialog를 구현합니다.

```java
AlertDialog.Builder(context)
        .setTitle("제목")
        .setMessage("내용")
        .setPositiveButton("예",
                DialogInterface.OnClickListener { dialog, id ->
                    // Yes
                })
        .setNegativeButton("아니오",
                DialogInterface.OnClickListener { dialog, id ->
                    // No
                })
```



다이얼로그는 ~이다

우리는 앱에 따라 풀스크린을 사용한다

[링크: 풀스크린 설정 방법]

하지만 풀스크린에서 다이얼로그를 띄우면 다이얼로그 윈도우 속성 때문에 풀스크린이 해제된다

[코드: 다이얼로그 윈도우 속성]

다이얼로그를 띄어도 풀스크린을 유지하려면 윈도우 속성이 바뀌지 않도록 해야 한다.

[코드: 윈도우 속성 변경]

다이얼로그를 사용할때마다 적용하면 코드가 더러우니 다이얼로그를 따로 만들자

[링크: 커스텀 다이얼로그 만드는 법]

위에거를 참고해서 만들겠다.

[코드: 해결 방법]

이렇게 하면된다.