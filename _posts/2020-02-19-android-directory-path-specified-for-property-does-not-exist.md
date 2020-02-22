---
layout: post
title: "Directory project/app/build/extrated-include-protos/main specified for property '$1' does not exist. 오류 해결"
tags: [다이얼로그, 풀스크린]
comments: true
---

## Directory project/app/build/extrated-include-protos/main specified for property '$3' does not exist.

잘 되다가 갑자기 빌드 중에 위와 같은 오류가 발생하는 경우가 있다.
이 문제를 해결하려고 찾아보는 중에 간단한 해결 방법을 알아냈다.

<br />

## 해결 방법

탐색기를 열어 project/app/build/extrated-include-protos 경로로 들어가보자.
아래처럼 main 폴더가 없을 것이다.

![Directory1]({{ site.baseurl }}/images/android/directory_path_specified_for_property_does_not_exist/directory1.png){: .center-image}*main 없음*

<br />

해당 오류는 해당 디렉터리를 찾을 수 없다고 하는 것이므로 main 폴더를 추가한다.

![Directory2]({{ site.baseurl }}/images/android/directory_path_specified_for_property_does_not_exist/directory2.png){: .center-image}*main 추가*

<br />

그리고 다시 빌드하면 오류없이 정상적으로 빌드가 진행될 것이다.

<br />