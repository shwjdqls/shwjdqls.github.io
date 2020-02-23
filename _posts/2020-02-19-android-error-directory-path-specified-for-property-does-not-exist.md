---
layout: post
title: "[Android] Directory project/app/build/extrated-include-protos/main specified for property '$1' does not exist 오류 해결 방법"
category: Android
tags: [Android, Error]
comments: true
---

안드로이드 개발 중에 어느 날 갑자기 아래 에러 메시지를 보여주며 빌드가 되지 않았다.

Directory project/app/build/extrated-include-protos/main specified for property '$3' does not exist.

이 문제를 해결하려고 찾아보는 중에 간단한 해결 방법을 알아냈다.

<br />

## 해결 방법

탐색기를 열어 project/app/build/extrated-include-protos 경로로 들어가보자.
아래처럼 main 폴더가 없을 것이다.

![Directory1]({{ site.baseurl }}/images/android/error_directory_path_specified_for_property_does_not_exist/directory1.png){: .center-image}

<br />

해당 오류는 해당 디렉터리를 찾을 수 없다고 하는 것이므로 main 폴더를 추가한다.

![Directory2]({{ site.baseurl }}/images/android/error_directory_path_specified_for_property_does_not_exist/directory2.png){: .center-image}

<br />

그리고 다시 빌드하면 오류없이 정상적으로 빌드가 진행될 것이다.

<br />