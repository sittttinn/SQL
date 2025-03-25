# 2.3 데이터 탐색(Select, from, where)

### 도입: 포켓몬으로 SELECT 이해하기

포켓몬 이름, 타입등으로 이해할 수 있음

- 포켓몬 이름이 있으면 꼬부기를 선택해서 포켓몬을 선택할 수 있음
- 공격력을 확인해서 선택할 수 있음

→ 테이블 형태로 바꿔서 생각해보기

## 1. 쿼리구조

- select/ from/ where

### select

- 테이블의 어떤 컬럼을 선택할 것인가?
- Col1 AS new_naem, (별칭)

### From Dataset.Table

- 어떤 테이블에서 데이터를 확인할 것인가

### Where

- 만약 원하는 조건이 있다면 어떤 조건인가?
- Col1 =1: 조건문

### EX: 포켓몬 타입이 불인 포켓몬을 찾는 SQL 쿼리

```sql
SELECT
*
FROM basic.pokemon
WHERE
	type1="Fire"
```

*: 모든 컬럼을 출력하겠다

- 많이 사용하지 않음
- 행이 적으면 크게 이슈가 없음
- 데이터 확인할 때 미리보기를 활용함

```sql
SELECT
	*EXCEPT(제외할 칼럼)
```

이런 형태도 가능

- 컬럼의 수가 많을 때 유리
- join에서 활용

### 집합처럼 생각해보기

- 특정 table에 있는 데이터를 추출
- join을 활용해서 연결하기

### 실습

- 프로젝트 명시하면 불편하기 때문에 프로젝트를 제외하고 사용해도 됨.
- 그러나 여러개 프로젝트를 할 때는 주의해야 함
- 프로젝트 id를 제외하고 작성할 때는 ‘없어도 됨

```sql
SELECT
	*EXCEPT(enlish_name)
	id.AS pokemon_id. #별칭을 지어줄 때 AS를 활용한다
	kor_name
	type1.
	total
FROM basic.pokemon
WHERE
	type1 ="Fire"
```

- 데이터를 활용하고 싶은 목적이 있어야 칼럼을 선택할 수 있음
- AS에서 이름을 붙일 때, 컬럼 이름에 “”를 넣는 경우: 자주하는 실수 ⇒ 따옴표 없이 기록
- 커리를 작성할 때 tap를 쳐서 작성함
    - 커리를 잘 읽을 수 있으려면 잘 작성해야 함
    - 가독성이 높게 작성해야 협업할 때 좋음
- ; 단위로 커리문을 구분함
    - 하나의 커리가 끝남을 의미
    - 모든 결과에서 각 커리별 결과를 볼 수 있음
    - 웬만하면 단일커리로 작성하려고 하지만 처음 연습하는거면 다 해보기

|  | 실행순서 | 설명 |
| --- | --- | --- |
| FROM | 1: 어떤 테이블에 있는지 확인 | 데이터를 확인할 table명시
이름이 너무 길다면 AS 별칭으로 별칭 지정 가능 |
| WHERE | 2: 필터링 조건 | FROM에 명시된 TABLE 저장된 데이터 필터링(조건 설정)
TABLE에 있는 컬럼 조건 설정 |
| SELECT | 3: 선택해서 출력 | TABLE에 저장되어 있는 컬럼 선택
여러 컬럼 명시 가능
col1 AS 별칭으로 컬럼의 이름도 별칭 지정 가능 |

- 자주 하는 실수

SELECT → WHERE → FROM

불가능

# 2.4 SELECT 연습문제

## 문제1

train데이터의 모든 데이터를 보여주는 sql 쿼리

- 어떤 데이터가 있는지
- 어디에 명시해야 할까
- 필터링 조건이 있는가 → 필터링 할 필요가 없다
- 모든 데이터 → 모든 데이터 = 모든 컬럼일 수도 있겠다(추측)

```sql
SELECT
	*
FROM 
```

- 결과 정렬은 쿼리 결과에서 할 수 있음

## 문제2

trainer테이블의 트레이너 name 출력

- trainer 테이블 사용
- name 컬럼 사용

```sql
SELECT
	name
FROM basic.trainer
```

## 문제3

name과 age컬럼 출력

```sql
SELECT
	name, age
FROM basic.trainer
```

### 문제4

id가 3인 트레이너의 이름과 나이 hometown까지 출력

```sql
SELECT
	name,
	age,
	hometown
FROM basic.trainer
WHERE
	id=3
```

- 현업에서는 컬럼의 의미와 필요한 정보를 파악해서 작성해야함

→ 어떤 컬럼을 요구하는지 파악하는 과정이 중요함

### 문제5

pokemon 테이블에서 피카츄의 공격력과 체력을 확인할 수 있는 쿼리를 작성해주세요

```sql
SELECT
	attack
	hp
FROM basic.pokemon
WHERE
	kor_name = "피카츄"
```

# 2.5 집계(Group_by + having + sum/count)

## 데이터 활용 과정

조건 → 추출 → 변환 → `요약`

**집계:**

그룹화 해서 계산한다

### 집계: group_by

- 색상 기준으로 모은다 → group_by
- 중복이 제거됨
- 특정 컬럼을 기준으로 모으면서 다른 컬럼에서 집계 가능(합, 평균, max, min)
- 타입을 기준으로 그룹화해서 평균 공격력 집계하기
- 타입을 기준으로 그룹화해서 타입 별 포켓몬 수 집계하기
- 평균 공격력이 제일 높은 타입이 궁금한 경우
    - 내림차순으로 정렬
- 타입 당 포켓몬 수가 10마리 이상인 데이터만 추출하고 싶은 경우
- 집계후 - having

## sql로 표현하기

```sql
SELECT
	집계할 컬럼1
	집계함수
FROM Table
GROUP BY
	집계할 컬럼1
```

- 집계할 컬럼을 SELECT에 명시하고 그 컬럼을 꼭 GROUP BY에 작성
- 공식 홈페이지에서 확인하기

## DISTINCT: 고유값을 알고 싶은 경우

- distinct의 뜻: 별개의
- 여러 값 중에 unique한 것만 보고 싶은 경우 사용

- distinct는 중복값을 제거하는 것

```sql
SELECT
	집계할 컬럼
	COUNT(DISTINCT count할-컬럼)
FROM table
GROUP BY
	집계할 컬럼
```

**메인 페이지 view 수는? 4번**

COUNT(user_id)

**메인 페이지 view한 유저의 수는? 3번**

COUNT(DISTINCT user_id)

view와 유저의 수를 항상 같이 봄

필요할 때 적절한 것 사용

### 연습문제1

- **pokemon 테이블에 있는 포켓몬 수를 구하는 쿼리를 작성해주세요**

```sql
SELECT
	count(id)
FROM basic.pokemon
```

테이블 전체 기준으로 하는거면 이렇게 해도 상관없음

별칭을 새우지 않으면 f0이 됨

필요할 때마다 선택해서 사용하면 됨

별보다는 id를 명시하는게 좋음

### 연습문제2

- 포켓몬의 수가 세대별로 얼마나 있는지 알 수 있는 쿼리를 작성해주세요
- 사용할 테이블: pokemon
- 조건: x
- 그룹화를 할 대 사용할 컬럼: 세대
- 집계할 때 사용할 계산 : 얼마나 있는지 → 수를 구한다 → count

```sql
SELECT
	generation
	COUNT(id) AS cnt
FROM basic.pokemon
GROUP BY
	generation
```

### 그룹화 활용 포인트

- 데이터 분석하다가 그룹화하는 경우
    - 일자별 집계: 원본 데이터는 특정 시간에 어떤 유저가 한 행동이 기록, 일자별로 집계
    - 연령대별 집계: 특정 연령대에서 더 많이 구매했는가
    - 특정 타입별 집계: 특정 제품 타입을 많이 구매했는가
    - 앱 화면별 집계: 어떤 화면에 유저가 많이 접근했는가
- 무궁무진하다 ~!

### 조건을 설정하고 싶은 경우: where 과 having

- **WHERE**
    - table에 바로 조건을 설정하고 싶은 경우
- **HAVING**
    - group by한 후 조건을 설정하고 싶은 경우 사용

```sql
SELECT
	컬럼1, 컬럼2
	COUNT(컬럼1) AS col1_count
FROM <table>
GROUP BY 컬럼1, 컬럼2
HAVING
	col1_count>3
```

[서브쿼리]

- SELECT문 안에 존재하는 SELECT 쿼리
- FROM 절에 또 다른 SELECT 문을 넣을 수 있음
- 괄호로 묶어서 사용

```sql
SELECT
	*
FROM(
	SELECT
		컬럼1, 컬럼2
		COUNT(컬럼1) AS col1_ count
	FROM <table>
	GROUP BY 컬럼1, 컬럼2
)
WHERE
	col1_count>3
```

- 쿼리의 from절에 다른 쿼리가 들어간다
- having을 안 쓰려면 이렇게 해도 됨

[같이 쓰는 경우]

```sql
SELECT
	컬럼1, 컬럼2
	COUNT(컬럼1) AS col1_count
FROM <table>
WHERE
	컬럼1 >=3
GROUP BY 컬럼1, 컬럼2
HAVING
	COL1_COUNT >3
```

## 정렬하기: ORDER BY

```sql
SELECT
COL
FROM
ORDER BY <컬럼>, <순서>
```

순서:

- DESC: 내림차순
- OSC: 오름차순

*ORDER BY는 쿼리의 맨 마지막에 두고, 쿼리의 맨 마지막에만 작성하면 됨(중간에 필요없음)

## 출력 개수 제한하기: LIMIT

- 쿼리문의 결과 ROW 수를 제한하고 싶은 경우 LIMIT 사용
- 쿼리문의 제일 마지막에 작성

```sql
SELECT
	COL
FROM <TABLE>
LIMIT 10
```

### 연습문제

- 포켓몬 수를 타입 별로 집계하고, 포켓몬의 수가 10이상인 타입만 남기는 쿼리를 작성해주세요. 포켓몬의 수가 많은 순으로 정렬해주세요

- 조건: 테이블 원본 - 없음
- 집계 후 조건(HAVING) : 10이상
- 포켓몬의 수가 많은 순으로 정렬: 내림차순

```sql
SELECT
	TYPE1
	COUNT(ID) AS CNT
FROM BASIC.TABLE
GROUP BY
	TYPE1
HAVING CNT >= 10
ORDER BY CNT DESC
```

- 단계적으로 실행해보면 된다

---

## 정리하며..

- 집계하고 싶은 경우: GROUP BY + 집계 함수(AVG, MAX)
- 고유값을 알고 싶은 경우: DISTINCT
- 조건을 설정하고 싶은 경우: WHERE/ HAVING
- 정렬하고 싶은 경우: ORDER BY
- 출력 개수를 제한하고 싶은 경우: LIMIT


<img width="409" alt="image" src="https://github.com/user-attachments/assets/96973df5-fa00-46cf-8f33-b9fbd1bad1e5" />
