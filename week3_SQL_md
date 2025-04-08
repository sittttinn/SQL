# 3.4 오류를 디버깅하는 방법

## SQL 쿼리를 작성하다가 만나는 다양한 오류

- 쿼리를 작성하면서 실행이 안되는 경우를 경험할 것

Symtax error: 문법 오류

### 오류

오류 메시지를 보면서 더 좋은 길로 가자

### BigQuery Error

**📌Syntax Error(문법 오류)**

- 문법을 지키지 않아 생기는 오류
- error message을 보고 번역 또는 해석, 해결 방법 찾아보기

### BigQuery Error: Select list must not be empty at [10:1]

- 오류 메시지 번역: select 목록은 [10:1]에서 비어 있으면 안됩니다.

### chatGPT와 함께 진행하는 오류 디버깅

- 데이터 예시나 쿼리를 제공하고, 오류가 발생한 것을 말해보기
- 그 후에 또 다른 시도 후, 오류가 발생한다고 말하기

![image](https://github.com/user-attachments/assets/7fd679d6-962f-4b2b-be72-cc7ec123dde8)


[]()

### Number of arguments does not match for aggregate function COUNT

```sql
SELECT
	COUNT(id, kor_name)
FROM basic.pokemon
```

⇒ 집계함수 count의 인자 수가 일치하지 않습니다.

COUNT(kor_name, eng_name) ⇒ x

```sql
SELECT
	type1,
	COUNT(id) AS cnt
FROM basic.pokemon
```

- type1,에 느낌표!
- select 목록 식은 다음에서 **그룹화되거나 집계되지** 않은 열을 참조합니다.
- group by에 적절한 컬럼을 명시하지 않았을 경우 발생하는 오류

```sql
SELECT
	type1,
	COUNT(id) AS cnt
FROM basic.pokemon;

SELECT
	*
FROM basic.trainer;
```

Syntax error: Expected end of input but got keyword SELECT at [8:1]

- 입력이 끝날 것으로 예상되었지만 SELECT키워드가 입력되었습니다.

[줄, 칸]

- 여러 개를 작성하고 싶을 때 ;을 넣으면 됨

```sql
SELECT
	*
FROM basic.trainer LIMIT 10
WHERE
	id = 3
```

- Syntax error: Expected end of input but got keyword WHERE at [4:1]
- limit이 맨 마지막에 나와야 함
- 삭제하거나 맨 아래로 옮기기

```sql
SELECT
	name,
FROM (
	SELECT
		*
	FROM basic.trainer
	WHERE
		id = 3
```

- Syntax error: Expected ”)” but got end of  script at [8:11]

![image](https://github.com/user-attachments/assets/3dd80d5b-ff35-4cde-aec6-48d28b2c6e31)


# 4. 데이터 탐색 - 변환

---

- 데이터 탐색: 변환
- 자료형에 따른 여러 함수 소개
    - 문자열
    - 날짜 및 시간 데이터
- 조건문 함수
- BigQuery 공식 문서 확인하는 방법

## 4-2 데이터 타입과 데이터 변환(CAST, SAFE_CAST)

- SELECT 문에서 데이터를 변환시킬 수 있음
    - 또는 WHERE의 조건문에도 사용할 수도 있음
- 데이터의 타입에 따라 다양한 함수가 존재

```sql
SELECT
	컬럼1,
	컬럼2,
	컬럼3
FROM 테이블
WHERE <조건문>
GROUP BY <집계할 컬럼>
```

AS

```sql
SELECT
	컬럼1,
	컬럼2,
	컬럼3
```

### ✏데이터 타입

- 숫자: 정수이외도 포함
- 문자: “나”, “데이터”
- 시간, 날짜: 2024-01-01 23:43:21
- 부울: 참/거짓

(array, json등이 있음)

사용할 기본 타입 먼저 학습 → 그 후에 확장

### ✏데이터 타입이 중요한 이유

- 보이는 것과 저장된 것의 차이가 존재
    - 엑셀에서 보면 빈 값 ⇒ “”일 수도 있고, NULL일 수도 있음
    - 1이라고 작성된 경우 ⇒ 숫자 1일수도 있고, 문자 1일 수도 있음
    - 2023-12-31 ⇒ DATE 2023-12-31일 수도 있고, 문자일 수도 있음
- 내 생각과 다른 경우 데이터의 타입을 서로 변경해야 함

### ✏자료 타입 변경하기

- 자료 타입을 변경하는 함수: CAST

```sql
SELECT
	CAST(1 AS STRING) #숫자 1을 문자 1로 변경
	
SELECT
	CAST("카일스쿨" AS INT64)
```

- 숫자로 변경하려고 해도 불가능 → 오류 발생
- 더 안전하게 데이터 타입 변경하기: SAFE_CAST
- SAFE_가 붙은 함수는 변환이 실패할 경우, NULL 반환
    - SAFE_라고 붙으면 에러는 안뜸

### ✏수학 함수

- 수학 함수는 수학 연산(평균, 표준편차, 코사인 등)이 존재
- 카테고리가 나와있는데, 삼각함수, 지수, 버리기 등등.. 다양하게 존재
    - 암기할 필요는 없고, 생각보다 적게 사용함
    - 사용하는 것 위주로 생각하기
- x/y대신 SAFE_DIVIDE함수 사용하기
    - x, y중 하나라도 0인 경우 그냥 나누면 zero error가 발생

## 4-3 문자열 함수(CONCAT, SPLIT, REPLACE, TRIM, UPPER)

### ✏문자열(string) 함수

- 문자열: “안녕하세요”, “카일스쿨”
- 대표적인 연산

| 연산 | input | output | 함수 이름 |
| --- | --- | --- | --- |
| 붙이기 | 안녕 + 하세요 | 안녕하세요 | CONCAT |
| 분리하기 | “가, 나, 다, 라” | “가”, “나”, “다”, “라” | SPLIT |
| 수정하기 | 안녕하세요 | 실천하세요 | REPLACE |
| 자르기 | 안녕하세요 | 안녕 | TRIM |
| 변환 | ab | AB | UPPER |

**📈CONCAT**

```sql
SELECT
	CONCAT("안녕", "하세요") AS result
```

- from이 없는데 어떻게 동작하지?
    - concat 인자로 string이나 숫자를 넣을 대는 데이터를 직접 넣어준 것 → FROM이 없어도 실행

---

**📈SPLIT**

```sql
SELECT
	SPLIT("가, 나, 다, 라",", ") AS result
```

- split(문자열_원본, 나눌 기준이 되는 문자)
- 배열로 나옴(array타입) - 여러가지 데이터를 저장할 수 있는 데이터 자료 구조
- 1에 모든 데이터가 연결되어있으면 배열

---

**📈REPLACE**

```sql
SELECT
	REPLACE("안녕하세요", "안녕", "실천") AS result
```

- replace(문자열 원본, 찾을 단어, 바꿀 단어)

---

**📈TRIM**

```sql
SELECT
	TRIM("안녕하세요", "하세요") AS result
```

- TRIM(문자열 원본, 자를 단어)

---

**📈UPPER**

```sql
SELECT
	UPPER("abs") AS result
```

- Function not found: UPPERT; did you mean upper? at [30:3] : 이 함수가 없다 ⇒ 오타를 의미
- upper(문자열 원본)
- trainer ⇒ 이름이 Ash ⇒ 어떤 곳에서는 ash, 모두 대문자로 변경해서 같은지 체크해야 할 수 있음(id)

## 4-4 날짜 및 시간 데이터 이해하기(1)

### ✏ 날짜 및 시간 데이터의 핵심

- 우리가 아는 일반적인 시간 ≠ 개발 시간
    - 1월 1일 한시
    - 개발 시간은 다름 - 시차가 존재하는 것처럼
- 데이터 타입 파악: DATE, DATETIME, TIMESTAMP
- 관련 알면 좋은 내용: **UTC**, Millisecond
- 타입 변환하기
- 시간 함수(두 시간의 차이, 특정 부분 추출하기)
    - 연도만 추출하고 싶은 경우 등 여러가지 시간을 추출할 수 있음

### ✏ 시간 데이터 다루기

- 시간 데이터도 세부적으로 나눌 수 있음
- date, datetime, timestamp등
- DATE: DATE만 표시하는 데이터, 2023-12-31
- DATETIME: DATE와 TIME까지 표시하는 데이터, Time Zone 정보 없음, 2023-12-31 14:00:00
- Time: 날짜와 무관하게 시간만 표시하는 데이터

### ✏ 시간 데이터 다루기 - 타임존

- GMT: greenwich mean time
    - 영국의 그리니치 천문대로 시간의 구분선
    - 한국 시간: GMT +9
- UTC: universal time coordinated
    - 국제적인 표준 시간
    - 협정 세계시
    - 타임존이 존재 = 특정 지역의 표준 시간대
    - UTC+9
- TIMESTAMP
    - 시간 도장
    - **UTC**부터 경과한 시간을 나타내는 값
    - time zone 정보 있음
    - 2023-12-31 14:00:00 UTC

### ✏ 시간 데이터 다루기 - millisecond, microsecond

- millisecond
    - 시간의 단위, 천 분의 1초
    - 우리가 아는 초보다 더 짧은 시간 단위
    - 눈을 깜빡이는 시간이 약 100ms
    - 빠른 반응이 필요한 분야에서 사용(초보다 더 정확하게)
    - millisecond ⇒ timestamp ⇒ datetime으로 변경
- microsecond(us)
    - 1/1000ms, 1/1,000,000초
- 예시
    - 1704176819711ms
    - 2024-01-02 15:26:59
    
    ```sql
    SELECT
    	TIMESTAMP_MILLIS(1704176819711) AS milli_to_timestamp_value,
    	TIMESTAMP_MICROS(1704176819711000) AS mirco_to_timestamp_value,
    	DATETIME(TIMESTAMP_MICROS(1704176819711000)) AS milli_to_timestamp_value;
    ```
    
- 타임존이 없음

```sql
SELECT
	TIMESTAMP_MILLIS(1704176819711) AS milli_to_timestamp_value,
	TIMESTAMP_MICROS(1704176819711000) AS mirco_to_timestamp_value,
	DATETIME(TIMESTAMP_MICROS(1704176819711000)) AS datetime_value
	DATETIME(TIMESTAMP_MICROS(1704176819711000), "Asia/Seoul") AS datetime_value;
```

- **타임존을 꼭 쓰기 !!**

### ✏ 시간 데이터끼리의 변환

- 많은 회사들이 table에 시간이 timestamp로 저장된 경우가 많음
- timestamp <=> datetime 변환을 해야 할 수 있음

 

### ✏ timestamp와 datetime 비교

- 24년 1월 18일 20시 55분(한국 시간)에 실행

```sql
SELECT
	CURRENT_TIMESTAMP() AS timestamp_col
	DATETIME(CURRENT_TIMESTAMP(), "Asia/Seoul") AS datetime_col
```

|  | timestamp | datetime |
| --- | --- | --- |
| 타임존 | UTC나옴 | UTC없음 - 대신 t를 사용 |
| 시간차이 | 한국시간 - 9 | 한국zone사용시 한국 시간과 동일 |

![image](https://github.com/user-attachments/assets/c9f988b2-ec18-482f-9f43-7a9055228c4e)
