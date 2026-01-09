## [입양 시각 구하기]

>핵심 개념: GROUP BY, DATE

### 문제 요약
동물 보호소에서 09:00~19:59 사이 각 시간대별 입양 건수 조회

### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| animal_id | 각 동물의 ID |
| animal_type | 생물종 |
| datetime | 입양일 |
| name | 이름 |
| sex_upon_outcome | 성별 및 중성화 여부 |
---

### 접근 방식
1. WHERE 조건식에서 입양일 시간(HOUR)이 9시에서 19시이도록 조건 필터링
   (HOUR 함수 사용시 19:59도 19로 출력되기 때문)
2. 시간대별 집계값을 구하기 위해 시간대별로 GROUP BY 실행
3. HOUR 함수를 이용해 시간대 출력, 집계 함수 COUNT를 이용해 그룹별 입양 건수 출력
4. 시간대 순으로 정렬


### 시행착오
- DATE 관련 함수인 HOUR 함수 개념 숙지하지 못함

---

### 최종 쿼리
```sql
SELECT  HOUR(DATETIME) AS HOUR,
        COUNT(*) AS 'COUNT'
FROM ANIMAL_OUTS
WHERE HOUR(DATETIME) BETWEEN 9 AND 19
GROUP BY HOUR
ORDER BY HOUR;
