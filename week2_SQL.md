## 데이터 탐색 - 조건, 추출, 요약 연습 문제(1)

### 1. 포켓몬 중에 type2가 없는 포켓몬의 수를 작성하는 쿼리를 작성해주세요

~가 없다: 컬럼 IS NULL

- 조건: type2가 없는
    - type2가 뭔지
    - null은 뭔가: 아무것도 없는 값, 아무것도 존재하지 않는 값
    - 값이 없는 상태
    - 연산자: IS NULL
    - type2 IS NULL
    - type2 IS NOT NULL
    - NULL은 IS 연산자를 사용한다.
- 어떤 테이블: pokemon
- 어떤 칼럼: 포켓몬의 수만 남기면 됨
- 어떻게 집계: 포켓몬의 수 -> count

```sql
SELECT
  count(id) AS cnt
FROM basic.pokemon
WHERE
  (type2 IS NULL)
  OR (type1 = "FIRE")
```

- where 절에서 여러 조건을 연결하고 싶은 경우 => AND 조건을 사용
- OR 조건 -> ( ) or ( )

### 2. type2가 없는 포켓몬의 type1과 type1의 포켓몬 수를 알려주는 쿼리를 작성해주세요.

단, type2의 포켓몬 수가 큰 순으로 정렬해주세요.

- 테이블: pokemon
- 조건: type2가 없는 포켓몬
- 컬럼: type1
- 집계: 포켓몬 수 => count
- 정렬: type2의 포켓몬 수가 큰 순으로 정렬 -> ORDER BY. 큰 순으로: 큰 것부터 작은 것 -> 내림차순(DESC) -> ORDER BY 포켓몬 수 DESC

```sql
SELECT
  type1
  COUNT(id) AS pokemon.cnt
  -- 집계 함수는 group by 의 같이 다님. 집계하는 기준(칼럼)이 없으면 count만 쓸 수 있으나, 집계하는 기준이 있다면 그 기준 칼럼을 group by에 써줘야 한다.
FROM basic.pokemon
WHERE
  type2 IS NULL
GROUP BY
  type1
ORDER BY
  pokemon.cnt DESC
```

### 3. type2 상관없이 type1의 포켓몬 수를 알려주는 쿼리를 작성해주세요

- 테이블: pokemon
- 조건: type2 상관없이 → 조건인가 아닌가 생각하기
- 컬럼: type1
- 집계 기준: 포켓몬 수 → count

```sql
SELECT
	type1.
	COUNT(id) AS pokemon.cnt
FROM basic.pokemon
GROUP BY
	type1

```

**COUNT(DISTINCT id) AS pokemon_cnt2**

- DISTINCT 언제 쓸까 -> 고유한 값만 보고 싶을 때 사용한다.Unique한 값만 알고 싶은 경우 사용
- COUNT(id) = COUNT(DISTINCT id)
- id를 설계할 때, 중복이 없게 설계. 그래서 두 개의 결과가 동일
- 어떤 칼럼은 중복이 있게 설계되곤 함
- distinct도 걸어서 보고 어떤 값이 더 맞을까?
- distinct: DAU(Daily Active User)
- Active한 유저의 수를 하루 단위로 집계
- COUNT(DISTINCT user_id) AS dau → 이런 데이터를 젖아하는 곳에 접속 기록, 이벤트 로그가 여러 row 가 존재 ⇒ Unique ⇒ DISTINCT

---

## 4. 전설 여부에 따른 포켓몬 수를 알 수 있는 쿼리를 작성해주세요

- 테이블: pokemon
- 조건: 없음
- 컬럼: 전설(is.legendary)
- 집계: 포켓몬 수

```sql
SELECT
	is.legendary.
	-- 컬럼 이름 앞부분 일부를 입력하고 기다리면 자동 완성
	COUNT(id) AS pokemon_cnt
FROM basic.pokemon
GROUP BY
	is.legendary
ORDER BY 2 DESC
```

- GROUP BY: is.legendary가 길다. GROUP BY에 컬럼이 많이 있을 수 있음(5개+COUNT)
- GROUP BY 1 ⇒ SELECT의 첫 칼럼을 의미
- count에 들어간 컬럼은 group by에 들어갈 수 없음
- ORDER BY에도 1, 2 등을 사용할 수 있음
- 1, 2 ⇒ 쿼리를 빠르게 작성하고, 결과를 보는 과정. 완성도니 쿼리문에서 1,2같은 표현보단 명확하게 칼럼을 명시하는 게 좋습니다(가독성)

## 5. 동명이인이 있는 이름은 무엇일까요? (한번에 찾으려고 하지 않고 단계적으로 가도 괜찮아요)

- 테이블: trainer
- 조건: 같은 이름이 두 개 이상 ⇒ count(name) ⇒ 2개 이상
- 컬럼: 이름
- 집계: count

```sql
SELECT
	name.
	COUNT(name) AS trainer_cnt
FROM basic.trainer
GROUP BY
	name
HAVING
	trainer_cnt => 2
```

- 집계 후 조건: having - group by와 함께 집계 결과에 조건을 설정하고 싶은 경우
- from절의 테이블 조건: where - 원본 데이터 FROM 절에 있는 데이터에 조건을 설정하고 싶은 경우

```sql
-- 서브커리: 쿼리문을 한번 감싸서 다른 쿼리문에서 사용할 수 있음
SELECT
*
FROM (
SELECT
	name
	COUNT(name) AS trainer_cnt
FROM basic.trainer
GROUP BY
	name
)
WHERE
	trainer_cnt =>
```

- having을 쓰면 쿼리 줄이 줄어든다

## 6. trainer 테이블에서 “Iris” 트레이너의 정보를 알 수 있는 쿼리를 작성해주세요

- 테이블: trainer
- 조건: 트레이너의 이름이 iris
- 칼럼: 정보 - 모든 컬럼
- 집계: 없음

```sql
SELECT
	*
FROM basic.trainer
WHERE
	name = "Iris"
```

---

## 7. trainer 테이블에서 “Iris”, “Whitney”, “Cynthia” 트레이너의 정보를 알 수 있는 쿼리를 작성해주세요.

*hint: 컬럼 IN (”Iris”, “Whitney”, “Cynthia”)

- 테이블: trainer
- 조건: 이름이 iris, whitney, cynthia
- 칼럼: 정보 → * 모든 컬럼
- 집계: 없음

```sql
SELECT

FROM basic.trainer
WHERE
	(name = "Iris") 
	or (name = "Whitney")
	or (name = "Cynthia")
-- or조건이 너무 길면 in을 사용
	name IN ("Iris", "Whitney", "Cynthia")
```

## 8. 전체 포켓몬 수는 얼마나 되나요?

- 테이블: pokemon
- 조건: 없음
- 컬럼: 없음
- 집계: COUNT(id)

```sql
SELECT
	COUNT(id) AS pokemon
FROM basic.pokemon
```

## 9.세대별로 포켓몬 수가 얼마나 되는지 알 수 있는 쿼리를 작성해주세요.

- 테이블: pokemon
- 조건:
- 컬럼: generation
- 집계: count

```sql
SELECT
	generation
	COUNT (id) AS pokemon
FROM basic.pokemon
GROUP BY
	generation
```

---

## 10. type2가 존재하는 포켓몬 수는 얼마나 되나요?

- 테이블: pokemon
- 조건: type2가 존재하는 ⇒ type2 IS NOT NULL
- 칼럼: x
- 집계: 포켓몬의 수 ⇒ count
- 전체 116명 ⇒ COUNT(id) ⇒ 동일한가?

```sql
SELECT
	COUNT(id) AS pokemon_cnt
FROM basic.pokemon
WHERE
	type2 IS NOT NULL
```

## 11. type2가 있는 포켓몬 중에 제일 많은 type1은 무엇인가요

- 테이블: pokemon
- 조건: type2가 있는
- 칼럼: type1
- 집계: count

```sql
SELECT
	type1.
	COUNT(id) AS pokemon_cnt
FROM basic.pokemon
WHERE
	type2 IS NOT NULL
GROUP BY
	type1
ORDER BY
	pokemon_cnt DESC
LIMIT 1
```

- LIMIT 1 : 1개만

## 12. 단일(하나의 타입만 있는) 타입 포켓몬 중 많은 type1은 무엇일까요?

- 테이블: pokemon
- 조건: 단일타입
- 칼럼: type1
- 집계: count

```sql
SELECT
	type1
	COUNT (id) AS pokemon_cnt
FROM basic.pokemon
WHERE
	type2 IS NOT NULL
GROUP BY
	type1
ORDER BY
	pokemon_cnt DESC
LIMIT 1
```

## 13. 포켓몬 이름에 “파”가 들어가는 포켓몬은 어떤 포켓몬이 있을까요?

*hint: 컬럼 like “파%”

- 테이블: pokemon
- 조건: name 에 “파”가 들어가는 포켓몬
- 칼럼: 어떤 포켓몬이 있을까요? name
- 집계: 없음

```sql
SELECT
	kor_name
FROM basic.pokemon
WHERE
	name LIKE "파%"
```

- 컬럼 LIKE “특정단어%” %는 앞에서 붙을 수 있고 뒤에서 붙을 수도 있음
- %파: 끝나는 단어, 파%: 파로 시작하는 단어, %파%: 파가 들어간 단어
- 문자열 컬럼에서 특정 단어가 포함되어 있는지 알고 싶은 경우에 like 활용

## 14. 뱃지가 6개 이상인 트레이너는 몇 명이 있나요?

- 테이블: trainer
- 조건: 뱃지가 6개 이상 (badge_count ≥ 6)
- 칼럼:
- 집계: trainer의 수 (count)

```sql
SELECT
	COUNT (id) AS trainer_cnt
FROM basic.trainer
WHERE
	badge_count >= 6
```

## 15. 트레이너가 보유한 포켓몬이 가장 많은 트레이너는 누구일까요?

- 테이블: trainer_pokemon
- 조건: 없음
- 컬럼: trainer_id
- 집계: 포켓몬의 수 (count)

```sql
SELECT
	trainer_id
	COUNT (pokemon_id)
```
