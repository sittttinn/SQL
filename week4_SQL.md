# 4-4. 날짜 및 시간 데이터 이해하기

---

### 🐣 DATETIME 함수 - CURRENT_DATETIME

- current_datetime([time_zone]): 현재 datetime 출력
    - timezone을 빼먹으면 date에서 차이가 날 수도 있다 ~~!!

```sql
SELECT
	CURRENT_DATE() AS current_date,
	CURRENT_DATE("Asia/Seoul") AS asia_date,
	CURRENT_DATETIME() AS current_datetime
	CURRENT_DATETIME("Asia/Seoul") AS current_datetime_asia;
```

- DATETIME에서 특정 부분만 출력하고 싶은 경우
    - 주문 데이터 타임에 일자별 주문, 월별 주문을 뽑을 때
    - 2024년에 얼마나 주문이 들어왔는가 → datetime에서 year로 extract한 뒤에 주문을 세기~

```sql
SELECT
	EXTRACT(DATE FROM DATETIME "2024-01-02 14:00:00") AS date, #2024-01-02
	EXTRACT(YEAR FROM DATETIME "2024-01-02 14:00:00") AS year, #2024
	EXTRACT(MONTH FROM DATETIME "2024-01-02 14:00:00" AS DATETIME) AS month,#1
	EXTRACT(DAY FROM DATETIME "2024-01-02 14:00:00" AS DATETIME) AS month, #2
	EXTRACT(HOUR FROM DATETIME "2024-01-02 14:00:00" AS DATETIME) AS hour, #14
	EXTRACT(MINUTE FROM DATETIME "2024-01-02 14:00:00" AS DATETIME) AS minute, #0
```

- 요일을 추출하고 싶은 경우
    - extract(DAYOFWEEK from datetime_col)
    - 첫날이 일요일인 [1,7] 범위의 값으로 반환

```sql
#만약 주말 데이터만 가지고 싶다면, 1과 7의 값만 뽑으면 됨 
#with IN, CASEWHEN 함수를 써서 1,7은 주말이다
SELECT
	EXTRACT(DAYOFWEEK FROM DATETIME "2024-01-21 14:00:00") AS day_of_week_sun
```

- DATE과 HOUR만 남기고 싶은 경우 ⇒ 시간 자르기
    - 시간대별 데이터를 확인하고 싶을 때 datetime_trunc를 많이 이용함

```sql
DATETIME_TRUNC(datetime_col,HOUR)
#2024-01-02 14:42:13 -> 2024-01-02 14:00:00가 됨

#예제
SELECT
	DATETIME "2024-03-02 14:42:13" AS original_data
	DATETIME_TRUNC(DATETIME "2024-03-02 14:42:13", DAY) AS day_trunc,
	#2024-03-02T00:00:00
	DATETIME_TRUNC(DATETIME "2024-03-02 14:42:13", YEAR) AS year_trunc,
	#2024-01-01T00:00:00
	DATETIME_TRUNC(DATETIME "2024-03-02 14:42:13", MONTH) AS month_trunc,
	#2024-03-01T00:00:00
	DATETIME_TRUNC(DATETIME "2024-03-02 14:42:13", HOUR) AS hour_trunc,
	#2024-03-02T14:00:00
```

- 문자열 → datetime 타입으로 바꾸고 싶을때
    - PARSE_DATETIME(’문자열의 형태’,’DATETIME 문자열’) AS datetime
    - 파씽: 문자열 → 타입 변환

```sql
SELECT
	PARSE_DATETIME('%Y-%m-%d %H:%M:%S', 'n2024-01-11 12:35:35') AS parse_datetime;
```

- DATETIME → 문자열 데이터로 변환
    - FORMAT_DATETIME

```sql
SELECT
	FORMAT_DATETIME("%c", DATETIME "2024-01-11 12:35:35") AS formatted;
```

- LAST_DAY
    - 마지막 날을 알고 싶은 경우: 자동으로 월의 마지막 값을 계산
    - 일요일 기준으로 많이 사용함
    - ISO_WEEK을 사용하는게 더 편할 수 있다(`ISOWEEK` 함수는 특정 날짜가 속한 ISO 주 번호를 반환)

```sql
SELECT
	LAST_DAY(DATETIME '2024-01-03 15:30:00') AS last_day,
	#2024-01-31
	LAST_DAY(DATETIME '2024-01-03 15:30:00', MONTH) AS last_day_month,
	#2024-01-31
	LAST_DAY(DATETIME '2024-01-03 15:30:00', WEEK) AS last_day_week,
	#2024-01-06, 요일을 지정하지 않으면 sunday로 나옴 -> 토요일
	LAST_DAY(DATETIME '2024-01-03 15:30:00', WEEK(SUNDAY)) AS last_day_week_sun,
	#2024-01-06, 일요일로 시작하면 토요일이 마지막 날
	LAST_DAY(DATETIME '2024-01-03 15:30:00', WEEK(MONDAY)) AS last_day_week_mon,
	#2024-01-07, 월요일로 시작하면 일요일이 마지막 날
```

- DATETIME_DIFF
    - 두 데이트타임의 차이를 알고 싶은 경우
    - DATETIME_DIFF(첫 DATETIME, 두번째 DATETIME, 궁금한 차이)

```sql
SELECT
	DATETIME_DIFF(first_datetime,second_datetime, DAY) AS day_diff1,
	#1187
	DATETIME_DIFF(second_datetime,first_datetime, DAY) AS day_diff2,
	#-1187, 첫번째 인자로 작은 것을 넣으면 마이너스가 나옴
	DATETIME_DIFF(first_datetime,second_datetime, MONTH) AS month_diff,
	#39
	DATETIME_DIFF(first_datetime,second_datetime, WEEK) AS week_diff,
	#170
FROM(
	SELECT
		DATETIME "2024-04-02 10:20:00" AS first_datetime,
		DATETIME "2021-01-01 15"
	)
```

# 4-5. 시간 데이터 연습문

---

## 1. 트레이너가 포켓몬을 포획할 날짜(catch_date)를 기준으로 2023년 1월에 포획한 포켓몬의 수를 계산

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 포켓몬의 수
- 쿼리 계산 방법: COUNT
- 데이터의 기간: 2023-01
- 사용할 테이블: trainer_pokemon
- join key: x
- 데이터의 특징: 직접 봐야함
    - catch_date: DATE타입
    - catch_datetime: UTC. timestamp타입
    
    ⇒ 컬럼의 이름은 datetime인데 timestamp 타입으로 저장되어 있다
    
    - utc를 계산했을 때 catch_date가 어떤 시간 기준으로 된 건지 확인해봐야함
    - catch_date_kr 이나, 컬럼의 설명(description)을 추가하기도 함
    - catch_date 컬럼 catch_datetime 컬럼을 비교 ⇒ DATETIME(catch_datetime, “Asia/Seoul”)
    - catch_date ≠ DATE(DATETIME(catch_datetime, “Asia/Seoul”))
    
    ⇒ 있다면 catch_date는 사용하기 어렵다
    

**컬럼의 이름만 믿지 말고 확인해보기 !!**

</aside>

```sql
SELECT
	COUNT(*)
FROM (
SELECT
	id,
	catch_date,
	DATE(DATETIME(catch_datetime,"Asia/Seoul")) As catch_datetime_kr_date
FROM basic.trainer_pokemon
)
WHERE
	catch_date != catch_datetime_kr_date
```

- 같지 않은 경우는 141건, 같은 경우는 236건

```sql
SELECT
	COUNT(DISTINCT id) AS cnt
FROM basic.trainer_pokemon
WHERE
	EXTRACT(YEAR FROM DATETIME(catch_datetime, "Asia/Seoul"))=2023 
	#catch_datetime은 timestamp로 저장 -> datetime으로 변경
	AND EXTRACT(MONTH FROM DATETIME(catch_datetime, "Asia/Seoul"))=1
	
#85
```

- 요청한 사람 또는 문제를 그대로 볼 경우에 틀릴 수 있다.
- 칼럼을 꼭 파악하고 쿼리를 작성하자 ~~!!

## 2. 배틀이 일어난 시간(battle_datetime)을 기준으로, 오전 6시에서 오후6시 사이에 일어난 배틀의 수를 계산해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 오전 6시-오후6시 배틀의 수
- 쿼리 계산 방법: count
- 데이터의 기간: 일자는 상관없고 오전 6시-오후6시 ⇒ battle_datetime
- 사용할 테이블: battle
- join key: x
- 데이터의 특징: 확인해보기
    - 의심해야하는 것: type 확인하기
    - winner_id= null: 무승부
    - battle_date: battle_datetime 기반으로 만들어진 date
    - battle_datetime: DATETIME
    - battle_timestamp: TIMESTAMP
    - battle_datetime 이랑 DATETIME(battle_timestamp, “Asia/Seoul”) 같은지 확인!
</aside>

```sql
SELECT
	-- id,
	-- battle_datetime,
	-- DATETIME(battle_timestamp, "Asia/Seoul") AS battle_timestamp_kr,
	COUNTIF(battle_datetime=DATETIME(battle_timestamp, "Asia/Seoul")) 
	AS battle_datetime_same_battle_timestamp_kr
	#97
	COUNTIF(battle_datetime!=DATETIME(battle_timestamp, "Asia/Seoul")) 
	AS battle_datetime_not_same_battle_timestamp_kr
	#0
FROM basic.battle
```

→ 진짜 신뢰할 수 있는 데이터구나 ~~!!

```sql
SELECT
	COUNT(DISTINCT id) AS battle_cnt
FROM basic.battle
WHERE
	EXTRACT(HOUR FROM battle_datetime)>=6
	AND EXTRACT(HOUR FROM battle_datetime)<=18
```

```sql
SELECT
	COUNT(DISTINCT id) AS battle_cnt
FROM basic.battle
WHERE
	EXTRACT(HOUR FROM battle_datetime) BETWEEN 6 and 18
```

- between a and b: a와 b 사이 있는 것을 반환

```sql
-- 2-1. 시간대별로 몇 건이 있는가
SELECT
	hour,
	COUNT(DISTINCT id) AS battle_cnt
FROM
	SELECT
		*,
		EXTRACT(HOUR FROM battle_datetime) AS hour
	FROM basic.battle
)
GROUP BY
	hour
ORDER BY
	hour
```

## 3. 각 트레이너별로 그들이 포켓몬을 포획한 첫 날(catch_date)을 찾고, 그 날짜를 ‘DD/MM/YYYY’ 형식으로 출력해주세요. (2024-01-01 ⇒ 01/01/2024)

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 날짜를 특정 형태로 변경
- 쿼리 계산 방법: date → 문자열. format_datetime + MIN(첫날은 제일 작은 값이므로)
- 데이터의 기간: x
- 사용할 테이블: trainer_pokemon
- join key: x
- 데이터의 특징: catch_date는 UTC기준의 데이터. 한국 기준으로 하려면 catch_datetime을 사용해야 한다
</aside>

```sql
SELECT
	trainner_id,
	min_catch_date,
	--원본과 확인하기 위해서
	FORMAT_DATE("%d/%m/%y", min_catch_date)AS new_min_catch_date
	-- "DD/MM/YYYY"
	-- format_datetime해도 똑같은 결과가 나옴
	SELECT
	-- 포획한 첫날 + 날짜를 변경
		trainer_id
		MIN(DATE(catch_datetime,"Asia/Seoul")) AS min_catch_date
	FROM basic.trainer_pokemon
	GROUP BY
		trainer_id
	ORDER BY
		trainer_id
	-- order by 위치: select 제일 바깥에서 1번만 하면 됨
	-- 모든 row를 확인해서 재정렬 -> 연산이 많이 소요 -> 시간이 오래 걸림
	-- 가장 바깥쪽에서 하기
```

## 4. 배틀이 일어난 날짜(battle_date)를 기준으로 요일별로 배틀이 얼마나 자주 일어났는지 계산해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 요일별로 배틀이 얼마나 자주 일어났는가
- 쿼리 계산 방법: 요일별로 count
- 데이터의 기간: x
- 사용할 테이블: battle
- join key: x
- 데이터의 특징: battle_date가 정상
    - 요일을 어떻게 추출할 것인가?
    - extract 함수
</aside>

```sql
SELECT
	day_of_week,
	COUNT(DISTINCT id) AS battle_cnt
FROM (
	SELECT
		*,
		EXTRACT(DAYOFWEEK FROM battle_date) AS day_of_week,
	FROM basic.battle 
)
GROUP BY
	day_of_week
ORDER BY
	day_of_week
```

## 5. 트레이너가 포켓몬을 처음으로 포획한 날짜와 마지막으로 포획한 날짜의 간격이 큰 순으로 정렬하는 쿼리를 작성해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 처음과 마지막의 diff 큰 순으로 정렬!
- 쿼리 계산 방법: 처음 포획한 날짜 + 마지막으로 포획한 날짜 → 차이를 구하고(datetime_diff) → 차이가 큰 순으로 정렬
- 데이터의 기간: x
- 사용할 테이블: trainer_pokemon
- join key: x
- 데이터의 특징: catch_date는 UTC 기반으로 만들어진 일자. catch_datetime을 사용
</aside>

```sql
SELECT
	*,
	DATETIME_DIFF(max_catch_datetime, min_catch_datetime, DAY) AS diff
	-- 날짜 하나를 찍고, 네이버 지도에서 d-day계산기 -> 내가 예상한 답과 같은지 확인
FROM(
SELECT
	trainer_id,
	MIN(DATETIME(catch_datetime, "Asia/Seoul")) AS min_catch_datetime,
	MAX(DATETIME(catch_datetime, "Asia/Seoul")) AS max_catch_datetime,
FROM basic.trainer_pokemon
GROUP BY
	trainer_id
)
ORFER BY
	diff DESC
```

# 4-6. 조건문(case when, if)

---

- **조건문**
    - 특정 조건이 충족되면 어떤 행동을 하자
    - 특정 조건이 참일때 A 아니면 B
    - 조건에 따른 분기 처리가 필요한 경우
- **사용하는 방법**
    - casewhen
    - if

### 🫀조건문 함수가 사용되는 이유

- 데이터를 하다보면 특정 카테고리를 하나로 합치는 전처리가 필요
- 컬럼을 자기가 원하는 형태로 조정할 수 있어야 함
- ex: 월화수목금 ← 시간 흐름에서 특정 요일 데이터를 볼 때
- 전처리를 할 때, 조건문이 중요!

### 🧌CASE WHEN

```sql
SELECT
	CASE
		WHEN 조건1 THEN 조건1이 참일 경우 결과
		WHEN 조건2 THEN 조건2이 참일 경우 결과
		ELSE 그 외 조건일 경우 결과
END AS 새로운_칼럼_이름
```

**🧞‍♂️예시: rock&ground type을 만들기**

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: type이 rock 또는 ground면, rock&ground 라고 수정
- 쿼리 계산 방법: casewhen
- 데이터의 기간: x
- 사용할 테이블: pokemon
- join key: x
- 데이터의 특징: type1,2 모두 고려해야 함
</aside>

```sql
SELECT
	new_type1,
	COUNT(DISTINCT id) AS pokemon_cnt
FROM(
	SELECT
		*,
		CASE
			WHEN type1 IN ("Rock", "Ground") 
			OR type2 IN ("Rock", "Ground") THEN "Rock&Ground"
			ELSE type1
			END AS new_type1
	FROM basic.pokemon
)
GROUP BY
	new_type1
```

## 🧶 조건문 CASE WHEN 순서

**🧞‍♂️예시: 공격력 기준으로 분류**

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: type이 rock 또는 ground면, rock&ground 라고 수정
- 쿼리 계산 방법: casewhen
- 데이터의 기간: x
- 사용할 테이블: pokemon
- join key: x
- 데이터의 특징: type1,2 모두 고려해야 함
</aside>

```sql
SELECT
	eng_name,
	attack,
	CASE
		WHEN attack>=50 THEN "Strong"
		WHEN attack>=100 THEN "Very Strong"
		-- 순서를 바꿔줘야 함!
		ELSE 'Weak'
		END AS attack_level
FROM basic.pokemon
```

- 순서대로 조건만 맞으면 바로 넘어감
    - 50 먼저 걸리면 그냥 100넘어도 strong 처리하고 넘어가게 됨
- 조건 1,2에 둘 다 해당되면 앞선 순서를 따름
- 문자열 함수(특정 단어 추출)에서 이슈가 자주 발생
    - 이 단어만 들어가면 이것으로 바꾸도록 한다 ! 라는 함수

## 🧛🏻‍♂️ 조건문 - IF

```sql
IF(조건문, Ture일 때의 값, False일 때의 값) AS 새로운_칼럼_이름

SELECT
	IF(1=1, '동일한 결과','동일하지 않은 결과')AS result1,
	-- 동일한 결과
	IF(1=2, '동일한 결과','동일하지 않은 결과')AS result2
	-- 동일하지 않은 결과
```

- 단일 조건일 경우 유용

# 4-7. 조건문 연습 문제

---

## 1. 포켓몬의 ‘Speed’가 70이상이면 ‘빠름’, 그렇지 않으면 ‘느림’으로 표시하는 새로운 칼럼 ‘Speed_Category’를 만들어주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: speed 칼럼을 사용해서 새로운 speed_category를 만들어야 함
- 쿼리 계산 방법: casewhen, if → 조건이 단일이면 if가 더 편함
- 데이터의 기간: x
- 사용할 테이블: pokemon
- join key: x
- 데이터의 특징
    - speed = integer
    - min: 5
    - max: 140
</aside>

```sql
SELECT
	id,
	kor_name,
	speed,
	IF(speed>= 70, '빠름','느림') AS Speed_Category
FROM badic.pokemon
```

## 2. 포켓몬의 type1에 따라 water, fire, electric 타입은 각각 물, 불, 전기로 그 외 타입은 기타로 분류하는 새로운 컬럼 type_korean을 만들어 주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: type1을 사용해서 특정 조건→ 값을 변경
- 쿼리 계산 방법: casewhen(여러 조건)
- 데이터의 기간: x
- 사용할 테이블: pokemon
- join key: x
- 데이터의 특징
    - type이 여러가지
</aside>

```sql
SELECT
	id,
	kor_name,
	type1.
	case
		when type1="Water" then "물"
		when type1="Fire" then "불"
		when type1="Electric" then "전기"
		ELSE "기타"
	END AS tyep1_Korean
FROM basic.pokemon
```

## 3. 각 포켓몬의 총점(total)을 기준으로, 300 이하면 ‘low’, 301에서 500 사이면 ‘medium’, 501이상이면 ‘high’로 분류해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: total 컬럼 → 조건에 맞는 값 변경, 모두 숫자
- 쿼리 계산 방법: casewhen
- 데이터의 기간: x
- 사용할 테이블: pokemon
- join key: x
- 데이터의 특징
    - total 칼럼: integer
</aside>

```sql
SELECT
	id,
	kor_name,
	total,
	CASE
		WHEN total >= 501 THEN "high"
		WHEN total between 301 AND 500 THEN "Medium"
		-- WHEN total < 300 THEN "Low"
	ELSE "Low"
	END AS total_grade
FROM basic.pokemon
WHERE
	total_grade = 'Low' #total_grade 없어서 작동 안됨
```

```sql
SELECT
	*
FROM (
	SELECT
		id,
		kor_name,
		total,
		CASE
			WHEN total >= 501 THEN "high"
			WHEN total between 301 AND 500 THEN "Medium"
			-- WHEN total < 300 THEN "Low"
		ELSE "Low"
		END AS total_grade
	FROM basic.pokemon
)
WHERE
	total_grade = 'Low' #total_grade 없어서 작동 안됨
```

## 4. 각 트레이너의 배지 개수를 기준으로 5개 이하면 beginner, 6개에서 8개 사이면 Intermediate, 그 이상이면 advanced로 분류해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: badge_count = 조건에 만족하는 값을 변경
- 쿼리 계산 방법: casewhen
- 데이터의 기간: x
- 사용할 테이블: trainer
- join key: x
- 데이터의 특징: x
</aside>

```sql
SELECT
	id,
	name,
	badge_count,
	CASE
		WHEN badge_count >=9 THEN "Advanced"
		WHEN badge_count BETWEEN 6 AND 8 THEN "Intermediate"
	ELSE "Beginner"
	END AS trainer_level
FROM basic.trainer
```

## 5. 트레이너가 포켓몬을 포획한 날짜(catch_date)가 ‘2023-01-01’ 이후이면 recent, 그렇지 않으면 old로 분류해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 포획한 날짜
- 쿼리 계산 방법: if
- 데이터의 기간: x
- 사용할 테이블: trainer_pokemon
- join key: x
- 데이터의 특징: catch_date는 UTC 기준, catch_datetime은 TIMESTAMP
</aside>

```sql
SELECT
	recent_or_old,
	COUNT(id) AS cnt
FROM(
	SELECT
		id,
		trainer_id,
		pokemon_id,
		catch_datetime
		IF(DATE(catch_datetime, "Asia/Seoul")> "2023-01-01", "Recent","Old")
		AS recent_or_old
	FROM basic_trainer_pokemon
)
GROUP BY
 recent_or_old
-- 모든 조건이 a로 변환한다. 모든 컬럼에 동일한 값을 추가하고 싶을 때 바로 문자로 작성
	"Recent" AS recent_value
```

## 6. 배틀에서 승자(winner_id)가 player1_id와 같으면 player 1 wins, player2_id와 같으면 player2wins, 그렇지 않으면 draw로 결과가 나오게 해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 승패 여부를 알 수 있는 컬럼 작서
- 쿼리 계산 방법: casewhen
- 데이터의 기간: x
- 사용할 테이블: battle
- join key: x
- 데이터의 특징: x
</aside>

```sql
SELECT
	id,
	winner_id,
	player1_id,
	player2_id,
	CASE
		WHEN winner_id=player1_id THEN "Player1 Wins"
		WHEN winner_id=player2_id THEN "Player2 Wins"
	ELSE "Draw"
	END AS battle_result
FROM basic.battle
```

# 4-8. 정리

---

## 컬럼 변환하기 정리

- 데이터 타입
    - 숫자: 사칙연산 (safe_divide)
    - 문자: concat, split, replace, trim, upper
    - 시간, 날짜: extract, datetime_trunc, parse_datetime
    - 부울(bool): casewhen, if

# 4-9. BigQuery 공식 문서 확인하는 법

---

### ⛑️ 개발 공식 문서

- 기술을 어떻게 사용하는지 문서
- 참고서 느낌이라 어려워보일 수 있지만 익숙해져야 함
- “기술명” + documentation 으로 검색
- bigquery는 구글 클라우드 제품이므로 클라우드 url을 찾을 수 있음
- google sql = big query
- 필요한 것 위주로 찾아보는게 유익!!
- notation rule도 확인할 수 있음

### 📯 공식 문서를 꼭 봐야하나?

- 정확하고 현재 문법인 경우를 찾기에 가장 정확함
- 기준점으로 살펴보기

### 어떻게 해야할지 모를 때,

- bigquery xxxxx라고 검색
- i want to change string to int in bigquery
- stack overflow
    - 개발계의 지식인
    - 연도 확인하기 (legacy sql 일 수도 있음)
- RSS feed에서 받아볼 수 있다
    - 슬랙에서 알람 받아볼 수 있음

---

---

## 4.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표:
- 쿼리 계산 방법:
- 데이터의 기간:
- 사용할 테이블:
- join key:
- 데이터의 특징:
</aside>

```sql
SELECT
	
FROM 
```

![IMG_DEB2D92A01F9-1](https://github.com/user-attachments/assets/710c74b3-63f0-42c8-ba43-01df3ea343af)
