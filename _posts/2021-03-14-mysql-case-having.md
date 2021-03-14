---
layout: post
title: "MySQL CASE, HAVING 정리"
date: 2021-03-14 15:57:00 +0900
categories:
  - mysql
comments: true
---
# 개요
MySQL의 `CASE`, `GROUP BY`, `HAVING`에 대해서 간략하게 정리하고 복잡했던 예시를 하나 소개한다.

# `CASE`
`CASE`는 다음과 같은 형태로 사용한다.

```mysql
CASE WHEN 조건 THEN 참값 ELSE 거짓값 END
```

'조건'에 새로운 CASE 절을 넣어서 사용할 수도 있다.

```sql
select
  (CASE
    WHEN MAX(CASE WHEN price IS NULL THEN 1 ELSE 0 END)
  THEN MAX(price) END) AS price
# 후략
```
- 'price 값 중에서 NULL이 있으면 NULL을 출력하고 NULL이 없으면 최댓값을 출력하라'는 의미
- 끝에 `AS joinAt`이 없으면 column 이름이 생기지 않음

# `GROUP BY`와 `HAVING`
`GROUP BY`는 결과를 묶어준다. 보통 다음과 같이 원하는 분류별로 개수를 셀 때 많이 활용되는 것 같다.

```mysql
SELECT `type`, count(*) FROM myTable GROUP BY `type`;
```
- 타입 별로 묶어서 개수를 세어줌

`HAVING`은 `GROUP BY`에 조건을 검사해서 일치하는 결과물만 출력해준다. 예를 들어서 다음과 같은 테이블에서

| id | type | price |
|----|------|-------|
| 1  | A    | NULL  |
| 2  | B    | 2000  |
| 3  | A    | 3000  |
| 4  | A    | 4000  |

`GROUP BY`만 있는 쿼리를 적용하면

```sql
SELECT *, COUNT(*)
FROM myTable
GROUP BY `type`;
```

아래와 같은 결과가 나온다.

| id | type | price | count(*) |
|----|------|-------|----------|
| 1  | A    | NULL  | 3        |
| 2  | B    | 2000  | 1        |

> - 묶이지 않은 column은 원래 결과에서 맨 위에 있는 값이 출력된다.

`HAVING`을 넣은 아래 쿼리를 적용하면

```sql
SELECT *, COUNT(*)
FROM myTable
GROUP BY `type` HAVING count(*) > 2;
```

결과는 다음과 같다.

| id | type | price | count(*) |
|----|------|-------|----------|
| 1  | A    | NULL  | 3        |

## `HAVING` 추가 예시
`HAVING`에 `MAX`, `MIN`을 사용하면 `GROUP BY`로 묶인 후가 아니라 **묶이기 전**에서 조건을 찾는다.

다시 이 테이블에서

| id | type | price |
|----|------|-------|
| 1  | A    | NULL  |
| 2  | B    | 2000  |
| 3  | A    | 3000  |
| 4  | A    | 4000  |

적용한 쿼리와 결과는 아래와 같다.

```sql
SELECT *, COUNT(*)
FROM myTable
GROUP BY `type` HAVING max(price) < 3000;
```

| id | type | price | count(*) |
|----|------|-------|----------|
| 2  | B    | 2000  | 1        |

> - type A의 price 값 중 가장 높은 값은 4000이므로 결과에서 제외
> - type B의 price 값 중 가장 높은 값이 2000이므로 조건에 부합

# 내가 해결한 문제와 쿼리
`GROUP BY`로 묶었을 때 price에서 `NULL` 값을 가진 row가 하나라도 있으면 NULL을 출력하고 아니면 최댓값을 출력하도록 하였다.

이를 적용한 테이블과 쿼리, 결과는 아래와 같다.

| id | type | price |
|----|------|-------|
| 1  | A    | NULL  |
| 2  | B    | 2000  |
| 3  | A    | 3000  |
| 4  | A    | 4000  |

```sql
SELECT id, `type`,
  (CASE WHEN MAX(CASE WHEN price IS NULL THEN 1 ELSE 0 END) = 0 THEN MAX(price) END) AS price
FROM myTable
GROUP BY `type`
HAVING (price IS NULL) OR (MAX(price) > 1000)
```

| id | type | price |
|----|------|-------|
| 1  | A    | NULL  |
| 2  | B    | 2000  |

