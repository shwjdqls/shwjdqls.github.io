---
layout: post
title: "[안드로이드] 풀스크린(Fullscreen) 화면 설정 - Lean back, Immersive, Sticky immersive"
category: Android
tags: [Android, Fullscreen, Lean back, Immersive, Sticky immersive, Status bar, Navigation bar]
comments: true
---

풀스크린(Fullscreen)은 비디오, 게임, 이미지 갤러리, 책, 프레젠테이션과 같은 곳에 주로 사용된다. 이를 통해 앱의 화면 공간을 최대화하여 컨텐츠에 사용자를 더욱 몰입시키고, 사용자가 실수로 앱을 종료하지 못하게 한다. 풀스크린에서 사용자가 쉽게 시스템 탐색을 할 수 없기 때문에 사용자 경험을 고려하여 풀스크린을 사용해야 한다.

 [공식 문서](https://developer.android.com/training/system-ui/immersive)에 따르면 풀스크린으로 만들기위한 세 가지 옵션인 Lean back, Immersive, Sticky immersive를 제공한다. 세 가지 옵션 모두 시스템 표시 줄(상태 바, 내비게이션 바)이 숨겨지고 모든 터치 이벤트를 수신하지만 시스템 표시 줄을 다시 볼 수 있는 방법에 차이가 있다.

<br />

### 1. Lean back

Lean back은 사용자가 화면과 크게 상호 작용하지 않는 환경을 위한 것으로, 사용자가 화면 아무 곳이나 누르면 시스템 표시 줄을 다시 볼 수 있다. 비디오 앱에서 주로 사용된다.

### 2. Immersive

Immersive는 사용자가 화면과 많이 상호 작용하는 환경을 위한 것으로, 사용자가 풀스크린을 벗어나기 위해서 시스템 표시 줄이 숨겨진 가장자리에서 스와이프를 하여야 한다. 게임이나 갤러리, 프레젠테이션 등에서 주로 사용된다.

### 3. Sticky immersive

Sticky Immersive는 Immersive와 비슷하지만 Immersive는 가장자리에서 스와이프를 할 때 앱이 제스처를 인식하지 못하지만, Sticky Immersive는 제스처를 인식할 수 있다. 따라서 게임처럼 스와이프를 많이 필요로 하는 앱에서 주로 사용된다.

풀스크린 적용 시 앱의 특성에 맞추어 위의 옵션을 설정하면 된다. 옵션 별로 풀스크린을 구현해보자. 

<br />

```kotlin
override fun onWindowFocusChanged(hasFocus: Boolean) {
    super.onWindowFocusChanged(hasFocus)
    if (hasFocus) setFullscreen()
}
```

풀스크린을 적용하기 위해서 먼저 액티비티의 onWindowFocusChanged() 메서드를 구현한다. 이 메서드는 액티비티의 포커스가 변경될 때 실행되는 함수로 hasFocus가 true이면 onCreate(), onStart(), onResume() 등이 실행된 경우로 보면 된다.

<br />

```kotlin
private fun setFullscreen() {
    window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LAYOUT_STABLE or
            View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION or
            View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN or
            // 하단 내비게이션 바 숨기기
            View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or
            // 상단 상태 바 숨기기
            View.SYSTEM_UI_FLAG_FULLSCREEN

            // Immersive 옵션을 적용하려면 아래 플래그를 추가한다.
            // View.SYSTEM_UI_FLAG_IMMERSIVE

            // Sticky immersive 옵션을 적용하려면 아래 플래그를 추가한다.
            // View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
}
```

다음으로 액티비티가 포커스를 가지는 경우에 풀스크린을 설정하는 메서드를 구현한다. 기본으로 Lean back 옵션이 적용되며, Immersive나 Sticky immersive 옵션을 적용하려면 한 줄을 추가하면 된다.

<br />

이제 풀스크린을 위한 설정이 완료되었다! 적용 전과 적용 후의 화면을 비교해보자.

![일반 화면]({{ site.baseurl }}/images/android/fullscreen/normal.png){: width="50%" height="50%"}{: .center-image}*풀스크린 적용 전*

![풀스크린]({{ site.baseurl }}/images/android/fullscreen/fullscreen.png){: width="50%" height="50%"}{: .center-image}*풀스크린 적용 후*

비교해보면 상단의 상태 바와 하단의 내비게이션 바가 사라진 것을 볼 수 있다.

<br />

상황에 따라 상태 바나 내비게이션 바를 숨기고 싶지 않을 수도 있다. 해당하는 부분의 플래그를 제거하면 확인이 가능하다. 상단 상태바는 View.SYSTEM_UI_FLAG_FULLSCREEN, 하단 내비게이션 바는 View.SYSTEM_UI_FLAG_HIDE_NAVIGATION이다.
            
![상태 바 이미지]({{ site.baseurl }}/images/android/fullscreen/status_bar.png){: width="50%" height="50%"}{: .center-image}*상단 상태바*

![내비게이션 바 이미지]({{ site.baseurl }}/images/android/fullscreen/navigation_bar.png){: width="50%" height="50%"}{: .center-image}*하단 내비게이션 바*

<br />

일반적으로 풀스크린을 사용하기 위해 BaseActivity에 풀스크린을 구현하고 적용할 액티비티에서 이를 상속받아 사용한다. BaseActivity를 구현해보자.

```kotlin
package com.jacob.fullscreen

import android.view.View
import androidx.annotation.CallSuper
import androidx.appcompat.app.AppCompatActivity

abstract class BaseActivity : AppCompatActivity() {

    @CallSuper
    override fun onWindowFocusChanged(hasFocus: Boolean) {
        super.onWindowFocusChanged(hasFocus)
        if (hasFocus) {
            setFullScreen()
        }
    }

    private fun setFullScreen() {
        window.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_LAYOUT_STABLE or
                View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN or
                View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or
                View.SYSTEM_UI_FLAG_FULLSCREEN or
                View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
    }
}
```

@CallSuper는 하위 클래스에서 해당 메서드를 오버라이드할 시 반드시 상위 클래스의 메서드를 호출하라는 의미이다. 이제 풀스크린을 적용할 MainActivity를 구현한다.

<br />

```kotlin
class MainActivity : BaseActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        ...
    }

    ...
}
```

위의 방법으로 원하는 액티비티에 쉽게 풀스크린을 적용할 수 있다.

<br />

앱에 따라 풀스크린을 적용할 수도 있고, 단순히 상태 바나 내비게이션 바만 숨길 수 있다. 풀스크린을 적용한다면 앱의 특성에 맞게 적절한 풀스크린 옵션을 사용하자.

<br />