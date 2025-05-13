# 5-1 INTRO

---

단일 자료 → 조건, 추출, 변환, 요약

다량의 자료 → **연결(join)** → 조건, 추출, 변환, 요약

# 5-2 JOIN 이해하기

---

## SQL JOIN

- SQL을 처음 배울 때 어려울 수 있는 부분 → 문제 많이 풀자..
- 간단하게 서로 다른 데이터 테이블을 연결

---

## 포켓몬으로 join 이해

- 트레이너: 포켓몬을 포획해 육성하는 사람들
- 포켓몬은 다양한 곳에서 나타남
- 트레이너는 포획한 포켓몬을 육성할 수 있음
    
    ⇒ 트레이너와 포켓몬 사이에 두 데이터를 연결할 수 있는 공통 값이 없음!
    

**⇒ 트레이너가 포획한 포켓몬 데이터**

(=트레이너와 포켓몬을 연결할 수 있음)

- 트레이너가 포획한 포켓몬 기준으로 트레이너 데이터를 연결하기(JOIN)
- **연결할 수 있는 KEY = trainer_id, id**

---

- 예: trainer_id 컬럼 기준으로 트레이너 데이터를 연결
    - join은 컬럼이 추가됨
    - 큰 데이블 = 작은 테이블의 합체본

---

**SQL JOIN**

- 간단하게 “서로 다른 데이터 테이블을 연결하는 것”
- 공통적으로 존재하는 컬럼(=key)이 있다면 join할 수 있음
- 보통 **id값**을 key로 많이 사용하고 특정 범위(ex: date)에 **join**도 가능

---

**join을 해야 하는 이유: 데이터 저장되는 형태에 대한 이해**

- 관계형 데이터베이스 설계시 정규화 과정을 거침
    - 중복을 최소화하게 데이터를 구조화
    - user table은 유저 데이터만, order table은 주문 데이터만
    - 따라서 데이터를 다양한 table에 저장해서 필요할 때 join해서 사용

<aside>
💡

중복된 데이터를 최대한 줄이고 싶어하므로 정규화 과정을 거친다!

</aside>

- 데이터 분석하는 관점에서 미리 join되어 있는 것이 좋을 수 있지만, 개발 관점에선 분리되어 있는 것이 좋음
- 데이터 웨어하우스(=창고)에서 join + 필요한 연산을 해서 데이터 마트를 만들어서 활용
    - 목적에 맞는 재료 미리 준비
    - raw를 한번 더 가공
        - 일자별
        - 주문 건수
        - 매출
        - 지역별 등등…
    - 다시 join해서 저장할 때 한번 더 가공하기도 함

# 5-3 다양한 JOIN 방법

---

## 다양한 SQL JOIN 방법

- (inner) join: 공통 요소만 연결
- left/right (outer) join: 왼쪽/오른쪽 테이블 기준으로 연결
- full (outer) join: 양쪽 기준으로 연결
- cross join: 두 테이블의 각각의 요소를 곱하기

처음에 어렵다면 left join만 주로 사용해도 충분

![image](https://github.com/user-attachments/assets/41a4875b-0b3c-4899-94ce-281b142f7c07)


![image](https://github.com/user-attachments/assets/a3a2b21c-dbc5-413c-90f2-72bc657b4e44)


# 5-4 JOIN 쿼리 작성하기

---

1. 테이블 확인 - 테이블에 저장된 데이터, 컬럼 확인
2. 기준 테이블 정의 - 가장 많이 참고할 기준 테이블 정의
    - row가 적으면서 데이터를 다 포함하고 있는 테이블)
3. JOIN KEY찾기 - 여러 table과 연결할 key(on) 정리
4. **결과 예상하기 - 결과 테이블을 예상해서 손, 엑셀로 작성(일종의 정답지 역할)**
5. 쿼리 작성/검증 - 예상한 결과와 동일한 결과가 나오는지 확인

## SQL 문법

- from 하단에 join할 table을 작성하고 on 뒤에 공통된 컬럼(key)를 작성

```sql
SELECT
	A.col1,
	A.col2,
	B.col11,
	B.col12
FROM table1 AS A
LEFT JOIN table AS B
ON A.key=B.key 
#Alias를 사용할 수 있음
```

- cross제외하고 다 on이 필요함

![image](https://github.com/user-attachments/assets/2796deb2-5473-4429-a67d-298617e8c8da)


```sql
SELECT
	tp.*,
	t.*

FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id=t.id
#join 추가
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id=p.id
```

```sql
SELECT
	tp.id,
	tp.trainer_id,
	tp.pokemon_id,
	#id_1 : id라는 결과가 중복이여서 이렇게 설정
	t.id,
	t.age,
	t.hometown
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id=t.id
#on: join key를 기입
```

```sql
SELECT
	tp.*,
	#id_1 : id라는 결과가 중복이여서 이렇게 설정
	t.* EXCEPT(id), #trainer_id => tp에 있으니 그걸 활용
	p.* EXCEPT #pokemon_id => tp에 있으니 그걸 활용
FROM basic.trainer_pokemon AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id=t.id
#on: join key를 기입
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
```

# 5-5 JOIN을 처음 공부할 때 헷갈렸던 부분

---

- 여러 join 중 어떤 것을 사용해야 할까?
- 여러 table을 왼쪽에 두고, 어떤 table이 오른쪽에 가야할까?
- 여러 table을 연결할 수 있는 걸까?
- 컬럼은 모두 다 선택해야 할까?
- NULL이 대체 뭐죠?

## 1) 여러 join 중 어떤 것을 사용해야 할까?

- 하려고 하는 작업의 목적에 따라 join을 선택해보기
    - 교집합: inner
    - 모두 다 조합: cross
    - 그게 아니라면 LEFT 또는 RIGHT: **LEFT를 추천**, 하나를 계속 활용하는 것을 추천
- 쿼리 작성 템플릿에 예상하는 결과를 작성하고, 중간 결과도 생각하면서 찾아보기

## 2) 어떤 table을 왼쪽에 두고, 어떤 table이 오른쪽으로 가야할까?

- left join의 경우
    - 기준이 되는 table을 왼쪽에 두기
- 기준에는 기준값이 존재하고 **우측에 데이터를 계속 추가**
- 주문한 유저
    - order table의 user_id를 확인하면 됨
    - order + user
- 주문하지 않은 유저
    - 어떤 유저는 주문이 없어서 null이 나옴
    - 주문 컬럼이 한번도 없는 사람들의 데이터도 필요

## 3) 여러 table을 연결할 수 있는 걹까

- join의 개수에 한계는 없음
- 너무 많이 join하고 있는지 확인
    - 헷갈리고 어려움
    - 보통 3-5개 정도 하는게 좋음
    - 더 많으면 중간 데이터를 만들기!

```sql
SELECT
	table_a.col1, #A라고 해도 됨
	table_b.col2 #B라고 해도 됨
FROM table_a #AS A
LEFT JOIN table_b #AS B
ON table_a.key=table_b.key
```

## 4) 컬럼은 모두 다 선택해야 할까?

- 컬럼 선택은 데이터를 추출해서 무엇을 하고자 하냐에 따라 다름
- join이 잘 되었나 확인하기 위해서 많은 컬럼을 선택해도 되나, 선별하는 것이 비용을 줄임
- id같은 값은 unique한지 확인하기 위해 자주 사용되므로 id는 자주 사용하는 편

```sql
SELECT
	table_a.*, #여기서 except로 제외할 수 있음
--맨 아래의 select에서는 칼럼을 명시하고 줄였기 때문에 
--그 위의 select에서는 *써도 줄인 값만 계산이 됨
	table_b.*
FROM table_a
LEFT JOIN table_b
ON table_a.key=table_b.key
```

## 5) NULL이 대체 뭐죠?

- null : 값이 없음
- 0이나 공백과 다르게 값이 아예 없는 것
- join에서 연결할 값이 없는 경우 나타남

## 😎 연습문제

### (1) 트레이너가 보유한 포켓몬들은 얼마나 있는지 알 수 있는 쿼리를 작성해주세요

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 포켓몬 이름이 얼마나 있는지 알고 싶다
- 쿼리 계산 방법: trainer pokemon(status가 active, training) + pokemon JOIN → 그후에 group by 집계(count)
- 데이터의 기간: x
- 사용할 테이블: trainer_pokemon, pokemon
- join key: trainer_pokemon. pokemon_id = pokemon_id
- 데이터의 특징:
    - 보유했다의 정의는 status가 active, training인 경우를 의미
    - released= 방출
</aside>

- **쿼리 과정**
1. trainer_pokemon에서 status가 active, training인 경우만 필터(where)
    1. 1)을 먼저 하는 것이 좋을까, 혹은 join을 하고 필터하는 것이 나을까?
    2. join할 테이블을 줄이고 (raw수를 줄이고..) join하는 것이 더 효율적임
    3. where안하고 join하면 모든 테이블을 조회하고 join해야 하므로 매우 비효율적
    4. active, training 조건을 먼저 join하기
    5. **table을 줄이는 것이 중요한가를 먼저 생각하기!!** 
2. 필터링한 결과를 pokemon table과 join
3. 2)의 결과에서 pokemon_name, COUNT(pokemon_id) AS pokemon_cnt

```sql
SELECT -- (2)
	tp.*,
	p.id,
	p.kor_name
	kor_name,
	COUNT(tp.id) AS pokemon_cnt
	
FROM (
-- 복잡하다. . -> with문을 활용하면 깔끔함
	SELECT --(1)
		id,
		trainer_id,
		pokemon_id,
		status
	FROM basic.trainer_pokemon
	WHERE
		status IN ('active', 'training')
	) AS trainer_pokemon
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id=p.id
-- SELECT FROM (JOIN) WHERE GROUP BY
WHERE 
	status IN ("Active","Training")
	-- 그러면 여기 where 쓰는 게 더 좋은거 아니었냐 
	1=1 #1=1은 무조건 true를 변환 "가"="가" => 모든 row를 출력해라
	AND status = "Active" #1=1이 없으면 앞에 and를 빼야하는데 번거롭다고 생각해서 그냥 넣음
	AND status = "Training"
	-- 쿼리를 작성할 때 값을 바꿔가면서 실행해야 함 
	-- => 빨리 주석처리하기 위해서 앞에서 true인 1=1을 넣고, AND 쓰고 빠르게 주석 처리
	-- => 먼저 데이터를 줄이고 하는 것이 더 좋다 !
GROUP BY kor_name
ORDER BY
	pokemon_cnt DESC
```

- **join에서 많이 발생하는 문제**
    - column name id is ambiguous at: id가 모호하다. 더 구체적으로 말해달라
    - 중복된 컬럼의 이름이 있으면 어떤 테이블의 컬럼인지 명시해야함!!
    - id ⇒ tp.id

### (2) 각 트레이너가 가진 포켓몬 중에서 ‘Grass’ 타입의 포켓몬 수를 계산해주세요 (단, 편의를 위해 type1 기준으로 계산해주세요)

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 트레이너가 보유한 포켓몬 중에서 grass 타입의 포켓몬 수를 알고 싶다
- 쿼리 계산 방법: 트레이너가 보유한 포켓몬 조건 설정 → grass타입으로 where 조건 걸어서 count
- 데이터의 기간: x
- 사용할 테이블: trainer_pokemon, pokemon
    - 언제 왼쪽에 둘까요?
    - 우리가 풀려고 하는 문제에서 어떤 테이블이 기준이 되어야 할까?
        - trainer_pokemon 왼쪽?
        - pokemon이 왼쪽?
    - trainer_pokemon 테이블
        - 트레이너가 잡은 히스토리가 저장된 테이블
        - 트레이너가 보유했던 포켓몬이 얼마나 있는지 알려줌
    - pokemon
        - 포켓몬의 메타 정보. 상품은 고정
        - 그 상품을 주문하면서 주문이 생김
        - 포켓몬의 정보가 저장됨
        - pokemon을 왼쪽에 두면 → pokemon 중 보유되지 않았던 포켓몬들은 trainer_pokemon에 없을 것 = **`null`**
        - trainer_pokemon을 왼쪽에 두면: 트레이너가 보유했던 포켓몬들을 기반으로 포켓몬 데이터만 추가 = **`null이 추가되지 않음`**
        - 기준이 되는 테이블은 내가 구하고자 하는 데이터가 어디에 가장 잘 저장되어 있는가
        - join할 수 있는 key같아보이는 것이 많은 테이블을 제일 왼쪽으로 둠(예외 있음)
- join key: trainer_pokemon. pokemon_id = pokemon.id
- 데이터의 특징: 1번 문제와 동일
</aside>

```sql

SELECT
	-- tp.*,
	p.type1
	COUNT(tp.id) AS pokemon_cnt
FROM(
	SELECT
		id,
		traineR_id,
		pokemon_id,
		status
	FROM basic.trainer_pokemon
	-- table "trainer_pokemon" must be qualified with a datest (e.g. dataset.table)
	WHERE
		status IN ("Active","Training")
) AS tp
LEFT JOIN basic.pokemon AS p
ON tp.pokemon_id = p.id
WHERE
	type1="grass"
GROUP BY
	type1
ORDER BY
	2 DESC #2대신에 pokemon_cnt도 가능
```

### (3) 트레이너의 고향(hometown)과 포켓몬을 포획한 위치(location)를 비교하여, 자신의 고향에서 포켓몬을 포획한 트레이너의 수를 계산해주세요.

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 트레이너 고향과 포켓몬의 포획 위치가 같은 트레이너의 수를 계산
- 쿼리 계산 방법: trainer(hometown), trainer_pokemon(location) JOIN → 트레이너의 수 count
- 데이터의 기간: x
- 사용할 테이블: trainer, trainer_pokemon
- join key: trainer_id = trainer_pokemon.trainer_id
    - 어디를 왼쪽에 써야할까? trainer_pokemon을 left로 할 듯
- 데이터의 특징: status상관없이 구해주세요
</aside>

```sql
SELECT
	*
FROM basic.trainer AS t
LEFT JOIN basic.trainer AS tp
ON t.id = t[.trainer_id
WHERE
	name="Goh"

```

```sql
SELECT
	*
FROM basic.trainer
WHERE
	name='Goh'
```

- trainer엔 특정 트레이너의 정보가 1개 들어있음(1 row = 1data)
- join을 하다보면 right에서 left의 기준에 여러개가 있을 때
- 데이터가 더 많아지는 것처럼 보여요
- trainer_pokemon에 Goh가 6마리 포켓몬을 가지고 있어서 이렇게 결과가 합쳐진 것
- LEFT에 메타 정보를 두면 헷갈릴 수 있음

```sql
SELECT
	COUNT(DISTINCT tp.trainer_id) AS trainer_cnt
-- 트레이너의 수 => 28명	
	COUNT(tp.trainer_id) AS trainer_cnt
-- 트레이너와 포켓몬이 같은 건이 43개
FROM basic.trainer AS t
LEFT JOIN basic.trainer AS tp
ON t.id = t[.trainer_id
WHERE
	current_health IS NULL
WHERE
	tp.location IS NOT NULL
	AND t.hometown = tp.location
-- trainer 중에 포켓몬을 잡아보지 못한 trainer가 있으면 
-- NULL 조건을 걸어줘야 함
-- 지금 데이터에는 trainer 테이블에 있는 trainer들은 모두 
-- 포켓몬을 잡아봐서 신경쓰지 않아도 됨
```

### (4) Master 등급인 트레이너들은 어떤 타입의 포켓몬을 제일 많이 보유하고 있을까요?

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: master등급의 트레이너들이 가장 많이 가지고 있는 타입
- 쿼리 계산 방법: trainer + pokemon + trainer ⇒ master 조건 설정(where) → type1 group by + count
- 데이터의 기간: x
- 사용할 테이블: trainer, pokemon, trainer_pokemon
- join key: trainer.id=trainer_pokemon, trainer_id=trainer_pokemon, pokemon_id
- 2번에 나오는 trainer_pokemon을 LEFT
- 데이터의 특징:
</aside>

```sql
SELECT
	type1,
	COUNT(tp.id) AS pokemon_cnt
FROM(
	SELECT
		id,
		trainer_id,
		pokemon_id,
		status
	FROM basic.trainer_pokemon
	WHERE
		status IN ("Active","Training")
	) AS tp
	LEFT JOIN basic.pokemon AS p
	ON tp.pokemon_id = p.id
	LEFT JOIN basic.trainer AS t
	ON tp.trainer_id=t.id
-- LEFT JOIN을 연속해서 2번 사용할 수 있다(N번도 사용 가능)
	WHERE
		t.achievement_level="Master"
	GROUP BY
		type1
	ORDER BY
		2 DESC
	LIMIT 1
```

### (5) 인천 출신 트레이너들은 1세대, 2세대 포켓몬을 각각 얼마나 보유하고 있나요?

<aside>
🤖

- 쿼리를 작성하는 목표, 확인할 지표: 인천출신 트레이너들이 가진 포켓몬들의 세대 구분
- 쿼리 계산 방법: trainer + trainer_pokemon + pokemon ⇒ incheon출신 where ⇒ 세대(generation)로 group by count
- 데이터의 기간: x
- 사용할 테이블: trainer, trainer_pokemon, pokemon
- join key: [trainer.id](http://trainer.id) = trainer_pokemon.trainer_id, [pokemon.id](http://pokemon.id) = trainer_pokemon.pokemon_id
- 데이터의 특징: 보유하다의 정의
</aside>

```sql
SELECT
	generation,
	COUNT(tp.id) AS pokemon_cnt
FROM(
	SELECT
		id,
		trainer_id,
		pokemon_id,
		status
	FROM basic.trainer_pokemon
	WHERE
		status IN ("Active","Training")
) AS tp
LEFT JOIN basic.trainer AS t
ON tp.trainer_id = t.id
LEFT JOIN basic.pokemon AS t
ON tp.pokemom_id = p.id
WHERE
	t.hometown = "Incheon"
GROUP BY
	generation
```

- `unrecognized name: tariner_pokemon at [22:4]`
    - trainer_pokemon이 아니라 basic.trainer_pokemon은 인식할 것
- AS로 tp Alias를 줬기에 tp라고 작성해야 함

- 만약에 세대가 점점 데이터 늘어나서 1,2세대가 아니라 3세대도 생기면 어떻게 할 것인가
- 3세대가 생기면 3세대도 나오게 해줘 ! → 쿼리를 그대로 사용
- 3세대가 생겨도 1,2세대만 나오게 해줘 ! → where 조건에 generation IN (1,2)
- 너무 어렵다…😂😂😂

# 5-7 정리

---

- join : 여러 table을 연결해야 할 때, 사용하는 문법
- key: 공통적으로 가지고 있는 컬럼

```sql
SELECT
	t1.col, t2.col2
FROM table1 AS t1
LEFT JOIN table2 AS t2
ON t1.key=t2.key
```

- join 종류
    - inner
    - left/right
    - full
    - cross
