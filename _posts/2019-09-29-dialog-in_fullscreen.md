---
layout: post
title: "[Android] 풀스크린으로 다이얼로그 띄우기"
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
