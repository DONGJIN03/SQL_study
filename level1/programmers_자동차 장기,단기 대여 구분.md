## [자동차 대여 기록에서 장기/단기 대여 구분]

> String, Date

### 문제 요약
- 대여 시작일이 2022년 9월에 속하는 기록 중에서 대여 기간이 30일 이상이면 '장기 대여', 미만이면 '단기 대여' 표시 칼럼(RENR_TYPE) 추가해 추출


### 데이터 구조
| 컬럼명 | 설명 |
|------|------|
| history_id | 자동차 대여 기록 ID |
| car_id | 자동차 ID |
| start_date | 대여 시작일 |
| end_date | 대여 종료일 |
---

### 접근 방식
1. 조건 필터링 WHERE에서 조건(해당 날짜) 필터링
2. CASE문을 이용해 조건에 따른 값 분류해 칼럼 추가
3. 대여기간 계산 (end_date - start_date + 1) / DATEDIFF 문 사용 가능
4. DATE_FORMAT을 이용해 날짜 형식 동일


### 시행착오
- DATE_FORMAT 내용을 숙지하지 못함
- 대여 기간 계산 시 종료일에서 시작일을 뺀 값에 **1을 더해야** 하는 사실을 놓침

---

### 최종 쿼리
```sql
SELECT HISTORY_ID,
       CAR_ID,
       DATE_FORMAT(START_DATE, "%Y-%m-%d") AS START_DATE,
       DATE_FORMAT(END_DATE, "%Y-%m-%d") AS END_DATE,
       CASE
            WHEN DATEDIFF(END_DATE, START_DATE) + 1 >= 30 THEN '장기 대여'
            ELSE '단기 대여'
       END AS RENT_TYPE
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
WHERE MONTH(START_DATE) = 09
ORDER BY HISTORY_ID DESC;
