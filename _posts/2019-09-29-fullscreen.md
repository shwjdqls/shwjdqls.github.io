---
layout: post
title: "[안드로이드] 풀스크린(Full screen) 화면 설정 완벽 정리 - 타이틀, 상태, 내비게이션 바 제거"
category: Android
tags: [풀스크린]
comments: true
---

풀스크린은 ~이다.

세 가지 옵션이 있다.
1. Lean back

2. Immersive

3. Sticky immersive

![fullscreen](https://user-images.githubusercontent.com/23516282/65833863-ea835180-e30f-11e9-8533-a3438d7814ac.png)
![normal](https://user-images.githubusercontent.com/23516282/65833864-ea835180-e30f-11e9-9c16-a7cc797357de.png)
![nav](https://user-images.githubusercontent.com/23516282/65833865-eb1be800-e30f-11e9-8056-3b21999c1dc2.png)
![full](https://user-images.githubusercontent.com/23516282/65833866-eb1be800-e30f-11e9-802f-6e6d13bc31d6.png)

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