---
layout: post
title: "[Android] 풀스크린으로 다이얼로그 띄우기"
category: Android
tags: [Dialog, Fullscreen, Custom, AlertDialog]
comments: true
---

## 다이얼로그란?

다이얼로그는 사용자에게 결정을 내리거나 추가 정보를 입력하라는 내용을 표시하는 작은 창으로 화면을 가득 채우지 않으며 사용자의 입력을 기다린다.

이를 사용하기 위해 Dialog의 서브 클래스인 AlertDialog를 사용하게 되며, 일반적으로 빌더 패턴을 이용하여 구현한다.

커스텀 다이얼로그는 만드는 방법이 궁금하다면 [여기](https://shwjdqls.github.io/android-custom-dialog/)를 참고하자.

<br />

## 풀스크린이란?

풀스크린은 상태바나 내비게이션바나 보이지 않는 전체 화면을 의미한다. 이는 여러 앱에서 사용되는데, 자세한 내용과 설정 방법은 [여기](https://shwjdqls.github.io/android-fullscreen/)를 확인!

<br />

## 그래서 뭐가 문제야?

아래 코드를 이용하여 풀스크린에서 다이얼로그를 한 번 띄워보자.

```java
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val dialog = AlertDialog.Builder(this)
                .setTitle("제목")
                .setMessage("내용")
                .setPositiveButton("예") { dialog, id ->
                    // Yes
                }
                .setNegativeButton("아니오") { dialog, id ->
                    // No
                }
                .create()

        dialogButton.setOnClickListener {
            dialog.show()
        }
    }
    
    override fun onWindowFocusChanged(hasFocus: Boolean) {
        super.onWindowFocusChanged(hasFocus)
        if (hasFocus) setFullscreen()
    }

    private fun setFullscreen() {
        window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LAYOUT_STABLE or
                View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN or
                View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_FULLSCREEN or
                View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
    }
}
```

![example1]({{ site.baseurl }}/images/android/dialog-in-fullscreen/fullscreen.png){: width="50%" height="50%" .center-image}*풀스크린이 잘 적용되었다 하지만..*

![example2]({{ site.baseurl }}/images/android/dialog-in-fullscreen/dialog-not-fullscreen.png){: width="50%" height="50%" .center-image}*다이얼로그가 뜨니 풀스크린이 해제된다*

<br />

## 풀스크린에서 다이얼로그 띄우는 방법

다이얼로그가 올라오면 다이얼로그의 윈도우 속성 때문에 풀스크린이 해제된다.
다이얼로그에서도 풀스크린을 유지하려면 다이얼로그의 윈도우 속성에서도 풀스크린 설정을 해야 한다.

```java
dialogButton.setOnClickListener {
    dialog.window?.let {
        it.setFlags(WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE, WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE)
        it.addFlags(WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM or WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON)
        it.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LAYOUT_STABLE or
                View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN or
                View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_FULLSCREEN or
                View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
        dialog.show()
        it.clearFlags(WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE);
    }
}
```
코드를 보면 let이라는 구문이 있는데, 이는 코틀린의 고유 문법으로 dialog.window가 널이 아닐때만 실행되며, it이 dialog.window를 가리킨다고 생각하면 된다.

![example2]({{ site.baseurl }}/images/android/dialog-in-fullscreen/dialog-fullscreen.png){: width="50%" height="50%" .center-image}*풀스크린에서 다이얼로그가 뜬 모습*

<br />

하지만 다이얼로그를 띄울 때마다 매번 이렇게 긴 코드를 사용할 수는 없다. 보통 다이얼로그는 커스텀하는 경우가 많으므로 [커스텀 다이얼로그](https://shwjdqls.github.io/android-custom-dialog/) 코드를 조금 수정해보겠다.

```java
// CustomDialog
fun show() {
    dialog = builder.create()
    dialog?.window?.let {
        it.setFlags(WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE, WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE)
        it.addFlags(WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM or WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON)
        it.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LAYOUT_STABLE or
                View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN or
                View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_FULLSCREEN or
                View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
        dialog?.show()
        it.clearFlags(WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE);
    }
}
```

<br />

이제 깔끔하게 사용 가능하다.

```java
// MainActivity onCreate()
val dialog = CustomDialog(this)
        .setTitle("제목")
        .setMessage("내용")
        .setPositiveButton("예") {
            Toast.makeText(this, "예", Toast.LENGTH_SHORT).show()
        }.setNegativeButton("아니오") {
            Toast.makeText(this, "아니오", Toast.LENGTH_SHORT).show()
        }

dialogButton.setOnClickListener {
    dialog.show()
}
```

![example2]({{ site.baseurl }}/images/android/dialog-in-fullscreen/custom-dialog-fullscreen.png){: width="50%" height="50%" .center-image}*커스텀 다이얼로그에 적용*

<br />