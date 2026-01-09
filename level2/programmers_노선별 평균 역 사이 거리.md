## [노선별 평균 역 사이 거리 조회하기]

>핵심 개념: 

### 문제 요약
  노선별로 노선, 총 누계 거리(역 사이 거리 총 합), 평균 역 사이 거리 조회

### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| line | 호선 |
| no | 순번 |
| route | 노선 |
| station_name | 역 이름 |
| d_between_dist | 역 사이 거리 |
| d_cumulative | 노선별 누계 거리 |

---
### 접근 방식
1. 노선별 집계 함수를 구하기 위해 route 를 그룹화
2. SELECT 문에 노선, 총 누계 거리, 평균 역 사이 거리 조회
3. 총 누계 거리는 그룹별 역 사이 총 합인 SUM() 함수 사용 후 소숫점 2의 자리에서 반올림,
  <br>평균 역 사이 거리는 그룹별 평균 AVG() 사용 후 소숫점 1의 자리에서 반올림
4. 두 개의 값에 CONCAT 함수를 이용해 'km' 단위를 문자열로 결합해주어 단위 표현
5. 총 누계 거리 기준으로 내림 차순 정렬을 위해 단위를 제외한 원래 계산 식 기준으로 작성
---

### 시행착오
- 정렬할때 SELECT 문에서 입력한 총 누계 거리 별칭을 이용하여 정렬해서 숫자형 기준 정렬이 아닌 단위인 km가 포함된 문자열 기준 정렬을 함.  

---

### 최종 쿼리
```sql
SELECT  ROUTE, 
        CONCAT(ROUND(SUM(D_BETWEEN_DIST), 1), 'km') AS TOTAL_DISTANCE,
        CONCAT(ROUND(AVG(D_BETWEEN_DIST), 2), 'km') AS AVERAGE_DISTANCE
FROM SUBWAY_DISTANCE
GROUP BY ROUTE
ORDER BY ROUND(SUM(D_BETWEEN_DIST), 1) DESC;
