## [대장균 크기에 따라 분류하기]

>핵심 개념: CASE, PERCENT_RANK, NTILE

### 문제 요약
  대장균 개체의 크기에 따라  상위 0% ~ 25% 를 'CRITICAL', 26% ~ 50% 를 'HIGH', 51% ~ 75% 를 'MEDIUM', 76% ~ 100% 를 'LOW' 라고 분류
  
### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| id | 대장균 개체의 id |
| parent_id | 부모개체의 ID |
| size_of_colony | 개체의 크기 |
| differentiation_date | 분화되어 나온 날짜 |
| genotype | 개체의 형질 |
---
### 접근 방식1 (PERCENT_RANK 사용)
1. 각 대장균 개체의 크기가 상위 몇퍼센트인지 나타내는 칼럼이 필요하다고 느껴 PERCEINT_RANK() 사용하여 개체의 크기를 내림차순으로 정렬 후 표현
2. 개체의 크기에 대한 순위 퍼센트를 포함한 테이블을 CTE로 나타낸 후 해당 테이블에서 CASE문을 이용해 이름 부여
3. CASE문은 비교 연산자를와 AND 연산자를 이용해 범위 표현
4. id 기준으로 오름차순 정렬
---

### 시행착오
- PERCENT_RANK()함수가 아닌 ROW_NUMBER() 함수를 사용하여 순위를 나타 낸 뒤 칼럼 수로 나누어 백분율을 나타내고자 함
  <br> -> ROW_NUMBER()는 각 행에 순서를 부여하는 방식으로 동일한 값에 대한 순위를 다르게 나타내므로 잘못됨
- RANK() 함수를 사용하여 칼럼 수로 나누어 백분율로 나타낼려고 했지만 늘어나는 서브쿼리와 테이블 조인으로 코드가 복잡해짐
- CASE문으로 개체 이름을 표현할 때 BETWEEN함수를 사용했으나 BETWEEN은 두 값 사이를 모두 포함 하는 경우이므로 실수로 구간을 표현할 때
  구간이 겹치거나 비는 실수 구간이 발생할 수 밖에 없음. (-> 해당값 초과 및 이하 비교 연산 사용)
---

### 최종 쿼리
```sql
WITH n AS (SELECT *, 
           PERCENT_RANK() OVER(ORDER BY size_of_colony DESC) AS num
FROM ecoli_data)
SELECT id,
       CASE
        WHEN num BETWEEN 0 AND 0.25 THEN 'CRITICAL'
        WHEN num > 0.25 AND num <= 0.5 THEN 'HIGH'
        WHEN num > 0.5 AND num <= 0.75 THEN 'MEDIUM'
        WHEN num > 0.75 AND num <= 1 THEN 'LOW'
       END AS colony_name
FROM n
ORDER BY id ASC;     
```
---
### 접근 방식2 (NTILE 사용)
1. 각 대장균 개체의 크기 순으로 4그룹으로 나누기 위해 내림차순 정렬 후 NTILE(4)를 이용해 분할 후 번호를 매김
2. 개체의 크기에 대한 분할 번호를 포함한 테이블을 CTE로 나타낸 후 해당 테이블에서 CASE문을 이용해 이름 부여
3. 정렬된 크기 순서대로 번호가 부여되었으므로 번호값 1~4 순서에 따라 크기순서대로 이름 부여
4. id 기준으로 오름차순 정렬
---

### 최종 쿼리
```sql
WITH n AS (SELECT *, 
           PERCENT_RANK() OVER(ORDER BY size_of_colony DESC) AS num
FROM ecoli_data)
SELECT id,
       CASE
        WHEN num BETWEEN 0 AND 0.25 THEN 'CRITICAL'
        WHEN num > 0.25 AND num <= 0.5 THEN 'HIGH'
        WHEN num > 0.5 AND num <= 0.75 THEN 'MEDIUM'
        WHEN num > 0.75 AND num <= 1 THEN 'LOW'
       END AS colony_name
FROM n
ORDER BY id ASC;     
```
