## [연도별 대장균 크기의 편차 구하기]

>핵심 개념: 

### 문제 요약
  분화된 연도별로 대장균의 크기의 편차(연도별 가장 큰 대장균 크기 - 각 대장균 크기)와 대장군 개체의 id 출력

### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| id | 대장균 개체의 id |
| parent_id | 부모 개체의 id |
| size_of_colony | 개체의 크기 |
| differentiation_date | 분화되어 나온 날짜 | 
| genotype | 개체의 형질 |
---
### 접근 방식
1. 대장균 편차의 크기를 구하기 위해 각 연도별 가장 큰 대장균 크기부터 구해야 함
2. 서브 쿼리를 이용해 분화되어 나온 날짜의 연도별로 그룹을 묶고 각 그룹별 개체의 크기 최대값과 연도를 구하는 테이블 생성
3. 서브쿼리를 이용해 만든 테이블과 기존 테이블을 분화되어 나온 날짜의 연도가 같은 기준으로 조인하면 연도별 대장균 최대 크기 칼럼이 추가된 테이블이 생성됨
4. 조인한 테이블에서 연도와 대장균 크기의 편차(연도별 대장균 크기 최댓값 - 대장균 크기), 개체 아이디 칼럼 출력
5. 연도 기준으로 정렬, 같은 경우 대장균 크기 편차 기준으로 정렬 
---

### 시행착오
- 서브쿼리에서 최댓값 테이블만 생성하고 조인을 위한 키값을 생성하지 않음

---

### 최종 쿼리
```sql
SELECT  YEAR(E.DIFFERENTIATION_DATE) AS YEAR,
        M.MAX - E.SIZE_OF_COLONY AS YEAR_DEV,
        E.ID
FROM ECOLI_DATA E INNER JOIN 
    (SELECT MAX(SIZE_OF_COLONY) AS 'MAX',
            YEAR(DIFFERENTIATION_DATE) AS YEAR
     FROM ECOLI_DATA 
     GROUP BY YEAR) M
ON YEAR(E.DIFFERENTIATION_DATE) = M.YEAR
ORDER BY YEAR ASC, YEAR_DEV;
