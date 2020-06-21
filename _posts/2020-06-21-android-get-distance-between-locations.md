---
layout: post
title: "[Android] 두 좌표(위도, 경도) 사이의 거리 구하기"
category: Android
tags: [Android, GPS, Location, Distance]
comments: true
---

지도나 위치 관련 처리를 하다 보면 두 좌표 사이의 거리를 구해야 하는 경우가 자주 생긴다. 바로 아래 코드를 살펴보자.

## 거리 계산 코드

```java
object DistanceManager {

    private const val R = 6372.8 * 1000
    
    /**
     * 두 좌표의 거리를 계산한다.
     *
     * @param lat1 위도1
     * @param lon1 경도1
     * @param lat2 위도2
     * @param lon2 경도2
     * @return 두 좌표의 거리(m)
     */
    fun getDistance(lat1: Double, lon1: Double, lat2: Double, lon2: Double): Int {
        val dLat = Math.toRadians(lat2 - lat1)
        val dLon = Math.toRadians(lon2 - lon1)
        val a = sin(dLat / 2).pow(2.0) + sin(dLon / 2).pow(2.0) * cos(Math.toRadians(lat1)) * cos(Math.toRadians(lat2))
        val c = 2 * asin(sqrt(a))
        return (R * c).toInt()
    }
}
```

봐도 무슨 내용인지 이해하기는 어렵지만, 거리 계산에 들어가는 수학적인 지식은 우리가 알 필요는 없다. 그냥 가져다 쓸 뿐.

거리 계산하는 여러 코드를 살펴보고 실행해봤는데 이 코드의 결과가 구글 지도에서 실제로 찍어본 결과와 가장 비슷했다.

<br />

거리 계산이 잘 되는지 지도로 한 번 테스트해보자

## 테스트 코드

```java
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        textView.text = DistanceManager.getDistance(37.638591, 127.026325, 37.643801, 127.031755).toString()
    }
}
```

![app]({{ site.baseurl }}/images/android/get-distance-between-locations/map.png){: width="70%" height="70%" .center-image}*구글 지도: 749m*

![map]({{ site.baseurl }}/images/android/get-distance-between-locations/app.png){: width="50%" height="50%" .center-image}*계산 결과: 751m*

지도의 좌표를 그대로 찍어 확인해보았고, 거의 비슷한 것을 확인할 수 있다.

<br />
