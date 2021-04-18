---
layout: post
title: "[Flutter] 키보드 숨기기"
category: Flutter
tags: [Flutter, Keyboard]
comments: true
---

특정 동작을 할 때 키보드를 숨겨야 할 떄가 있다. 이럴때는 어떻게 해야 할까?

다음 한 줄이면 쉽게 키보드를 숨길 수 있다.

```dart
FocusScope.of(context).unfocus();
```

<br />