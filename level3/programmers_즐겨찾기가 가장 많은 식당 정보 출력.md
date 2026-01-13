## [즐겨착기가 가장 많은 식당 정보 출력하기]

>핵심 개념: GROUP BY, 서브 쿼리, 조인, (윈도우 함수)

### 문제 요약
  음식 종류별 즐겨찾기가 가장 많은 식당은 음식 종류, id, 식당 이름, 즐겨찾기 수를 조회

### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| rest_id | 식당 id |
| rest_name | 식당 이름 |
| food_type | 음식 종류 |
| views | 조회수 |
| favorites | 즐겨찾기 수|
| parking_lot | 주차장 유무|
| address | 주소 |
|tel | 전화번호|
---
### 접근 방식1 (서브쿼리 및 조인 이용)
1. 음식 종류별 즐겨찾기 수가 가장 많은 식당 정보가 필요하므로 종류별 즐겨찾기 수의 최대값 정보가 필요함
2. 즐겨찾기 최대값과 식당 정보를 함께 표현하기 위해 테이블 조인을 이용하고자 함
3. 서브쿼리를 통해 음식 종류와 즐겨찾기 최댓값 테이블 생성 후 식당 정보 테이블과 조인
4. 필요한 정보 출력 및 음식 종류 기준으로 내림차순 정렬
---

### 시행착오
- 음식 종류 기준으로 그룹화 후 다른 정보값과 즐겨찾기 수 최댓값을 바로 출력할려고 함.
  <br> 이 경우 다른 정보 값들은 임의의 값을 반환하므로 즐겨찾기 수가 가장 큰 식당 정보가 아님
- **그룹화를 한 테이블만 가지고 그룹 내부 정보들을 특정 행을 출력하는건 불가능**
  <br> 서브쿼리 or 윈도우 함수 사용!!
  <br> **(그룹화는 집계결과만 출력할때, 윈도우 함수는 집계 결과를 추가 칼럼처럼 계산할때 사용)**
---

### 최종 쿼리
```sql
SELECT r1.food_type,
       r1.rest_id, 
       r1.rest_name,
       r2.favorites
FROM rest_info r1 INNER JOIN 
    (SELECT food_type,
      MAX(favorites) AS favorites
      FROM rest_info
      GROUP BY food_type) as r2
ON r1.favorites = r2.favorites AND r1.food_type = r2.food_type
ORDER BY r1.food_type DESC;
```
---
### 접근 방식2 (윈도우 함수 사용)
1. 음식 종류별 즐겨찾기 수가 가장 많은 식당 정보가 필요하므로 종류별 즐겨찾기 수의 최대값 정보가 필요함
2. 윈도우 함수를 이용해 종류를 분할 수 즐겨찾기수를 내림 차순 정렬하여 분할별로 행 번호를 매겨 최대값을 행 번호로 찾을려고 함
3. 식당 정보와 행 번호를 가진 테이블을 서브쿼리로 생성하여 테이블 사용
4. 행 번호가 1인 값(최대값)만 조건식을 이용해 필터링하여 즐겨찾기 수가 최대값인 행 필터링
5. 필요한 정보 출력 및 음식 종류 기준으로 내림차순 정렬
---

### 시행착오
- 윈도우 함수 실행 순서를 고려하지 못해 서브쿼리를 이용하지 않고 행번호 매긴 후 조건식에서 바로 최대값을 필터링 함
  <br> **SELECT 절에 있는 윈도우 함수 행 번호는 WHERE절 실행 이후 실행 되므로 조건식 사용시 실행되지 않음**
  <br> 서브쿼리를 이용해 행번호를 매긴 테이블 생성 후 조건식 필터링 사용
---

### 최종 쿼리
```sql
SELECT food_type, 
       rest_id, 
       rest_name, 
       favorites
FROM (SELECT *,
             ROW_NUMBER() OVER (
                 PARTITION BY food_type 
                 ORDER BY favorites DESC) AS rn
      FROM rest_info) t
WHERE rn = 1
ORDER BY food_type DESC;
```
