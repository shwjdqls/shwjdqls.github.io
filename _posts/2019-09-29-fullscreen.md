---
layout: post
title: "[안드로이드] 전체 화면(Fullscreen) 화면 설정 완벽 정리 - 상태 바, 내비게이션 바 제거"
category: Android
tags: [풀스크린]
comments: true
---

전체 화면은 비디오, 게임, 이미지 갤러리, 책, 프레젠테이션과 같은 곳에 주로 사용된다. 이를 통해 앱의 화면 공간을 최대화하여 컨텐츠에 사용자를 더욱 몰입시키고, 사용자가 실수로 앱을 종료하지 못하게 한다. 전체 화면에서 사용자가 쉽게 시스템 탐색을 할 수 없기 때문에 사용자 경험을 고려하여 전체 화면을 사용해야 한다.

 [공식 문서](https://developer.android.com/training/system-ui/immersive)에 따르면 전체 화면으로 만들기위한 세 가지 옵션인 Lean back, Immersive, Immersive sticky를 제공한다. 세 가지 옵션 모두 시스템 표시 줄(상태 바, 내비게이션 바)이 숨겨지고 모든 터치 이벤트를 수신하지만 시스템 표시 줄을 다시 볼 수 있는 방법에 차이가 있다.

1. Lean back

Lean back은 사용자가 화면과 크게 상호 작용하지 않는 환경을 위한 것으로, 사용자가 화면 아무 곳이나 누르면 시스템 표시 줄을 다시 볼 수 있다. 비디오 앱에서 주로 사용된다.

2. Immersive

Immersive는 사용자가 화면과 많이 상호 작용하는 환경을 위한 것으로, 사용자가 전체 화면을 벗어나기 위해서 시스템 표시 줄이 숨겨진 가장자리에서 스와이프를 하여야 한다. 게임이나 갤러리, 프레젠테이션 등에서 주로 사용된다.

3. Sticky immersive

Sticky Immersive는 Immersive와 비슷하지만 Immersive는 가장자리에서 스와이프를 할 때 앱이 제스처를 인식하지 못하지만, Sticky Immersive는 제스처를 인식할 수 있다. 따라서 게임처럼 스와이프를 많이 필요로 하는 앱에서 주로 사용된다.

전체 화면 적용 시 앱의 특성에 맞추어 위의 옵션을 설정하면 된다. 옵션 별로 전체 화면을 구현해보자.

```kotlin
override fun onWindowFocusChanged(hasFocus: Boolean) {
    super.onWindowFocusChanged(hasFocus)
    if (hasFocus) hideSystemUI()
}

private fun hideSystemUI() {
    // Enables regular immersive mode.
    // For "lean back" mode, remove SYSTEM_UI_FLAG_IMMERSIVE.
    // Or for "sticky immersive," replace it with SYSTEM_UI_FLAG_IMMERSIVE_STICKY
    window.decorView.systemUiVisibility = (View.SYSTEM_UI_FLAG_IMMERSIVE
            // Set the content to appear under the system bars so that the
            // content doesn't resize when the system bars hide and show.
            or View.SYSTEM_UI_FLAG_LAYOUT_STABLE
            or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
            or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
            // Hide the nav bar and status bar
            or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
            or View.SYSTEM_UI_FLAG_FULLSCREEN)
}
```

![fullscreen](https://user-images.githubusercontent.com/23516282/65833863-ea835180-e30f-11e9-8533-a3438d7814ac.png)



![normal](https://user-images.githubusercontent.com/23516282/65833864-ea835180-e30f-11e9-9c16-a7cc797357de.png)



![nav](https://user-images.githubusercontent.com/23516282/65833865-eb1be800-e30f-11e9-8056-3b21999c1dc2.png)



![full](https://user-images.githubusercontent.com/23516282/65833866-eb1be800-e30f-11e9-802f-6e6d13bc31d6.png)

