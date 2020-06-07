---
layout: post
title: "[AWS] AWS RDS MySQL 타임존, 한글 인코딩 설정"
category: AWS
tags: [AWS, RDS, MySQL, timezone, encoding]
comments: true
---

DATETIME 타입을 이용할 때 CURRENT_TIMESTAMP를 기본 값으로 자주 설정하게 된다.
이 떄 timezone이 맞지 않아 실제와 다른 시간이 나오는 경우가 있다.
아래 쿼리를 실행하면 현재 시스템의 시간을 알 수 있다.

```
SELECT NOW();
```

<br />

타임존을 현재에 맞게 설정해주어야 하는데 아래 쿼리로 현재 타임존을 확인해보자.
UTC로 되어있을 것이다.

```
SELECT @@global.time_zone
```

<br />

이를 간단하게 해결하기 위해서는 아래 쿼리로 값을 직접 바꿔주는 것이다.

```
SET GLOBAL time_zone='Asia/Seoul';
```

<br />

하지만 에러를 내뱉으며 실행이되지 않을 것이다.

Error Code: 1227. Access denied; you need (at least one of) the SUPER or SYSTEM_VARIABLES_ADMIN privilege(s) for this operation	0.000 sec

<br />

AWS RDS의 경우는 RDS에서 직접 타임존 설정을 해야 한다. 더불어 한글을 사용하기 위해서는 인코딩 설정을 해야 하는데 같이 설명하겠다.
방법은 매우 간단하게 글로 간략하게만 정리한다.

<br />

1) RDS 화면 -> 왼쪽 메뉴 파라미터 그룹 클릭 -> 파라미터 그룹 생성 -> 그룹 이름 지정 후 생성

2) 파라미터 선택 -> 파라미터 편집 -> 다음 이름에 값을 설정

- time_zone: Asia/Seoul

- character_set_client: utf8

- character_set_connection: utf8

- character_set_database: utf8

- character_set_filesystem: utf8

- character_set_results: utf8

- character_set_server: utf8

- collation_connection: utf8_general_ci

- collation_server: utf8_general_ci

3) 왼쪽 메뉴 데이터베이스 클릭 -> 원하는 데이터 베이스 선택 후 수정 -> DB 파라미터 그룹 변경

4) 데이터베이스 재시작

<br />

```
SELECT NOW();
```
위의 쿼리로 확인해보자. 이제 제대로 시간이 나올 것이다.