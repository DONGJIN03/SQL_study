## [분기별 분화된 대장균의 개체 수 구하기]

>핵심 개념: SELECT, QUARTER, GROUP BY

### 문제 요약
  각 분기에 'Q'를 붙이고 분기별 분화된 대장균의 개체의 총 수를 출력

### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| id | 대장균 개체의 ID |
| parent_id | 부모 개체의 ID |
| size_of_colony | 개체의 크기 |
| differntiation_date | 분화되어 나온 날짜 |
| genotype | 개체의 형질 |
---
### 접근 방식
1. SELECT문에서 분기를 계산하는 QUARTER문 사용 후 
2. CONCAT을 이용해 단위 'Q' 붙여준 뒤 QUARTER라는 별칭 사용
3. QUARTER 기준으로 그룹화 하여 분기별 대장군 개체 수 집계값 계산
4. 분기 기준으로 정렬
---

### 시행착오
- QUARTER 문이 아닌 CASE 문을 통해 직접 분기를 나누어 코드가 길어짐

---

### 최종 쿼리
```sql
SELECT  CONCAT(QUARTER(DIFFERENTIATION_DATE), 'Q') AS QUARTER,
        COUNT(ID) AS ECOLI_COUNT
FROM ECOLI_DATA
GROUP BY QUARTER
ORDER BY QUARTER;
