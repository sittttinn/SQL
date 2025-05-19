```
**목표**
데이터 결과 검증과 쿼리 효율화
```

# 6. 데이터 결과 검증

- 가독성을 챙기기 위한 SQL 스타일 가이드
- 데이터 결과 검증
- 데이터 결과 검증 예시
    
    ![image](https://github.com/user-attachments/assets/942b523e-9caa-436d-a371-d62054fd4953)

    

## 6.2 가독성을 챙기기 위한 SQL 스타일 가이드

### 📢 데이터 결과 검증을 하기 전에

**✅실수는 언제 발생하는가————-**

- 문법을 잘못 알고 있는 경우 : 문법 공부
- 데이터를 파악하지 않고 쿼리를 작성하는 경우: 데이터, 컬럼 정보, value에 대한 설명
- 쿼리가 복잡한 경우: 단순하게 보이도록 쪼개야 함

**✅다른 사람의 쿼리를 봐야하는 경우 또는 내 쿼리를 다른 사람이 보는 경우**

- 쿼리를 가독성있게 잘 작성 → 한번에 이해하도록 함
- 매번 설명할수록 비효율적
- 쿼리를 변경할 때 특정 부분만 바꿨는지, 전체를 바꿨는지도 파악하는 것이 쉬우면 좋음

### 🧨가독성을 챙기기 위한 SQL 스타일 가이드

- SQL 스타일 가이드
- mozilla(fire fox)의 SQL 가이드

1. 예약어는 대문자로 작성
    - SQL에서 문법적인 용도로 사용하고 있는 문자들은 대문자로 작성
    - 예약어의 대표적인 예시: SELECT, FROM, WEHRE 각종 함수

```sql
SELECT
	col
FROM table
WHERE
```

1. 컬럼 이름은 snake_case로 작성
    - 컬럼의 이름은 snake_case로 작성
    - **일관성이 중요!**

```sql
SELECT
	col1 AS event_status,
FROM table
```

1. 명시적 vs 암시적
    - Alias로 별칭을 지을 때는 명시적인 이름을 적용
    - AS a, AS b 등 컬럼의 의미를 한번 더 생각하게 하는 이름이 아닌 명시적인 것을 사용
    - JOIN할 때 테이블의 이름도 명시적으로 할 수 있다면 명시적으로 진행
    - AS를 생략해서 별칭을 설정할 수도 있는데, AS를 쓰는 것도 명시적인 표현
2. 왼쪽 정렬
    - 기본적으로 왼쪽 정렬을 기준으로 작성

```sql
SELECT
	col
FROM table
WHERE 1=1
```

1. 예약어나 컬럼은 한 줄에 하나씩 권장
    - 컬럼은 바로 주석 처리할 수 있는 장점이 있기에 한 줄에 하나씩 작성
2. 쉼표는 컬럼 바로 뒤에
    - 쉼표 앞 vs 뒤
    - big query는 마지막 쉼표를 무시해서 뒤로 작성해도 무방

```sql
SELECT
	col1,
	col2,
FROM table	
```

## 6.3 가독성을 챙기기 위한 WITH문 & 파티션

SQL 쿼리를 작성하다 생기는 일

- 만약 아래 쿼리가 다른 곳에서도 필요하면 복사 붙여넣기

```sql
SELECT
	col,col2
FROM(
	SELECT
		col,col2,col3...
	FROM
		table
```

🔽

```sql
WITH temp_table AS(
	SELECT
		col,col2,col3...
	FROM
		Table
)
SELECT
	col,col2
FROM temp_table
```

### with 구문

- CTE(common table expression)
- SELECT구문에 이름을 정해주는 것과 비슷하다
- 쿼리 내에서 반복적으로 쓸 수 있음

```sql
WITH name_a AS(
	SELECT
		col
	FROM Table
), name_b AS(
	SELECT
		col2
	FROM Table2
)
SELECT
	col
FROM name_b
```

- table 따로 저장 가능
- view ← 쿼리 덩어리 but 느림
- 상황에 따라 결정됨

### PARTITION

- table엔 partition이란 것이 존재할 수 있음
- 특정 시기에 들어온 물건을 찾고 싶다면?
    - 애초에 일자별로 정리하면 됨

### PARTITION을 사용하면 좋은 점

1. 쿼리 성능 향상
    - 전체 데이터를 스캔하는 것보다 파티션을 설정한 곳만 스캔하는 것이 더 빠름
2. 데이터 관리 용이성
    - 특정 일자의 데이터를 모두 변경하거나 삭제해야 하면 파티션을 설정해서 삭제할 수 있음
3. 비용
    - 파티션에 해당하는 데이터만 스캔해서 비용을 줄일 수 있음
    - big query는 쿼리 용량에 비례해서 과금

### PARTITION table: battle

- battle 테이블이 파티션으로 설정된 테이블
- 새 탭에서 열면 파티션이 자동으로 적용
- 비용 효율적으로 쿼리 실행 가능

### PARTITION table에서 쿼리하기

- where 절에 파티션 컬럼에 대해 조건을 설정해서 사용
- 2023-12-19부터 할 전날의 데이터를 추출하는 쿼리(저장된 데이터는 더 과거의 데이터라 이 쿼리를 돌릴 때 결과는 나오지 않음)
- 스키마> 세부정보>테이블 유형: 파티션
- 새 탭에서 열기

```sql
SELECT
	*
FROM 'inflearn-bigquery.basic.battle'
WHERE battle_datetime BETWEEN DATETIME("2022-01-26") 
AND DATETIME_ADD("2024-01-26", INTERVAL 1 DAY)
```

- 여기 나오는 용량= 탐색하는 범위

```sql
WITH sample AS(
	SELECT
		*
	FROM 'inflearn-bigquery.basic.battle'
), sample2 AS (
	SELECT
		id, name, hometown
	FROM basic.trainer
	WHERE
		id=3
), sample3 AS (
	SELECT
		*
	FROM sample2
)	
SELECT
	*
FROM sample
```

## 6.4 데이터 결과 검증 정의

갑작스럽게 데이터 추출을 해야하는 상황에 빠르게 데이터를 추출 후 공유

⇒ 결과 검증에 대한 내용

### 결과 검증을 잘하기 위한 마인드셋

데이터 검증을 하지 못해서 실수하는 것은 당연~

**다만 그 실수가 반복되는 것은 문제!**

### 데이터 결과 검증의 정의

- SQL 쿼리 얻은 결과가 예상과 일치하는지 확인하는 과정
- 목적: 분석 결과의 정확성, 신뢰성 확보
- 방법
    - 기대하는 예상 결과 정의
    - 쿼리 작성
    - 두개가 일치하는지 비교
- 제일 중요한 부분
    - 문제를 잘 정의하고 미리 작성
    - 도메인 특수성 잘 파악하기
    - SQL 쿼리 템플릿과 맥락이 유사

1. 문제 정의
    - 구체적인 문제 정의
    - 요청사항도 구체적으로 확인
2. input/output
    - 데이터의 input과 원하는 형태의 output 작성하기
    - input-”중간 결과”-output에서 중간 결과 생각하기
3. 쿼리 작성
    - 가독성 챙기기
4. 결과 비교
    - 예상과 실제 쿼리 결과의 차이가 있는지 확인
    - 오류가 있다면 다시 돌아감

### 데이터 결과 검증할 때 자주 활용하는 SQL쿼리

1. count(*): 행수를 확인, 의도한 데이터의 행 개수가 맞는가
2. not null: 특정 컬럼에 null이 존재하는가? 필수 필드가 비어있지 않은가(=데이터가 잘 저장되어있는가)
3. distinct: 데이터의 고유값을 확인해 **중복 여부 확인, count(distinct)=count()**
4. if문, case when: 의도와 같다면 true, 아니면 false

1)+3)

⇒ SELECT COUNT(DISTINCT col), COUNT(col) 두 컬럼을 보고 개수를 비교

### 데이터 결과 검증을 할 때 활용하는 방식

1. 특정 user_id로 필터링을 걸어서 확인
    1. 1명의 데이터 확인
    2. 결과를 예상할 떄 raw데이터에서 하나씩 세고 적음
    3. 1명이 데이터의 예상 결과과 쿼리 결과가 동일한지 확인
    4. 다른 user_id 3-4건 추가해서 확인
    5. 3-4개에서 동일한 결과가 나오면 user_id 조건을 삭제
2. 샘플 데이터 생성
    1. with문을 사용해 예시 데이터를 생성한 후, 결과를 예상하고 쿼리 작성
    2. 복잡한 데이터에서 하기 전에, 쿼리 자체가 올바른지 확인할 때 주로 사용
    3. union all: 세로로 아래로 붙인다

## 6.5 데이터 결과 검증 예시

### 트레이너 배틀 성적 분석, 각 트레이너가 진행한 배틀의 승리 비율 계산→ 9회 이상인 경우만 계산

- 승리 비율(=승리한 횟수/ 총 배틀 횟수)

⇒ 간단한 문제라서 이렇게 해도 틀릴 가능성이 적음

- 데이터 결과 검증을 한다면 어떻게 할 것인지?

```
**이상적인...데이터 결과 검증 프로세스**

1) 전체 데이터 파악
2) 특정 user_id 선정
3) 승률 직접 COUNT: 결과 예상
4) 쿼리 작성
5) 실제와 비교
6) 맞다면 특정 유저 조건 제외

```

1. 특정 user_id 선정
    - 특정 user_id(battle에선 playerN_id)를 선정
    - 회사에선 자신의 user_id를 사용하지만 여기선 임의로 선택
    
    ```sql
    SELECT
    	*
    FROM 'basic.battle'
    WHERE
    	(player1_id=7) or (player2_id=7)
    ```
    
2. 승률 직접 count: 결과 예상
    - player_id(player1_id, player2_id)가 7인 유저의 승률
    - 총 9회 중 5번 =5/9=0.5555
3. 쿼리 작성
    - 트레이너가 승리한 비율 구하기
        - 트레이너가 참여한 배틀의 수 구하기
        - 트레이너가 승리한 배틀의 수 구하기
        - 두개 조합해서 승리한 비율 구할 수 있음
        - 배틀의 수가 9 이상만 추출
    - 통합 데이터 생성(player1,2 구분을 하지 않아도 되는 테이블 → trainer_id 생성)

```sql
SELECT
	*
FROM(
	SELECT
	id AS battle_id,
	player1_id AS trainer_id,
	winner_id
FROM 'basic.battle'
UNION ALL
SELECT
	id AS battle_id,
	player2_id AS tariner_id,
	winner_id
FROM 'basic.battle'
)
ORDER BY battle_id
```

- trainer_id=7의 총 배틀 횟수를 구하는 쿼리

```sql
WITH battle_basic AS(
	SELECT
		id AS battle_id,
		player1_id AS trainer_id.
		winner_id
	FROM 'basic.battle'
	UNION ALL
	SELECT
		id AS battle_id,
		player2_id AS trainer_id,
		winner_id
	FROM 'basic.battle'
)
SELECT
	trainer_id,
	COUNT(*) AS total_battles,
	COUNT(DISTINCT battle_id) AS unique_battles
FORM battle_basic
WHERE trainer_id=7
GROUP BY
	trainer_id
```

1. 실제와 비교
- COUNTIF를 사용해 값 구하기

```sql
WITH battle_basic AS(
	SELECT
		id AS battle_id,
		player1_id AS trainer_id.
		winner_id
	FROM 'basic.battle'
	UNION ALL
	SELECT
		id AS battle_id,
		player2_id AS trainer_id,
		winner_id
	FROM 'basic.battle'
)
SELECT
	*,
	CASE,
		WHEN trainer_id=winner_id THEN "WIN"
		WHEN winner_id IS NULL THEN "DARW"
		ELSE "LOSE"
	END AS battle result
FROM battle_basic
--WHERE trainer_id=7
)
SELECT
	trainer_id,
	COUNT(battle_result='WIN') AS win_count
	COUNT(battle_id) AS total_battle_count
	COUNT(battle_result='WIN')/COUNT(DISTINCT battle_id) AS win_ratio
FROM battle_with_result
GROUP BY
	trainer_id
HAVING
	total_battle_count>=9
```

### 예시에서 예상한 결과

- trainer_ id=7일때 배틀의 횟수: 9
- 승리한 횟수: 5
- 승리한 비율: 0.555

## 6-6. 정리

![image](https://github.com/user-attachments/assets/c4420c93-f7f0-4606-b160-06a0ac2fcce9)

![image](https://github.com/user-attachments/assets/9dde61f8-775f-4a1d-a1ec-4e885cb8514d)
