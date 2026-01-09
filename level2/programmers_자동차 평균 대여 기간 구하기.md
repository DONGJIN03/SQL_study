## [자동차 평균 대여 기간 구하기]

>핵심 개념: GROUP BY, LIKE 패턴 추출

### 문제 요약
평균 대여기간이 7일 이상인 자동차들의 자동차 id, 평균 대여기간 리스트 출력

### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| history_id | 자동차 대여 기록 ID |
| car_id | 자동차 ID |
| start_date | 대여 시작일 |
| end_date | 대여 종료일 |
---
### 접근 방식
1. 자동차 id 별로 평균 대여기간을 구하기 위해 그룹화
2. 대여기간은 = (대여 종료일 - 대여 시작일 + 1)
3. HAVING절에서 대여 기간의 자동차 id별 평균 값이 7일 이상인 값만 필터링
4. 자동차 id, 대여기간 평균 값을 ROUND함수를 이용해 소수점 두 번째에서 반올림 한 값 추출
5. 평균 대여기간별 내림차순 정렬, 동일시 자동차 id 기준 내림차순 정렬
---

### 시행착오
- ROUND 함수 사용시 소수점 두 번째 자리에서 반올림 할 때 1이 아닌 2를 작성함.
  <br> 0번째 정수 자리. ROUND 사용시 **decimals 자리까지 반올림 함**.

---

### 최종 쿼리
```sql
SELECT  CAR_ID,
        ROUND(AVG(DATEDIFF(END_DATE, START_DATE)+1),1) AS AVERAGE_DURATION
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
GROUP BY CAR_ID
HAVING AVG(DATEDIFF(END_DATE, START_DATE)+1) >= 7
ORDER BY AVERAGE_DURATION DESC, CAR_ID DESC;
