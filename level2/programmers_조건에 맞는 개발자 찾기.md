## [조건에 맞는 개발자 찾기]

>핵심 개념: SELECT

### 문제 요약
  Python이나 C# 스킬을 가지 개발자의 ID, 이메일, 이름, 성을 조회

### 데이터 구조
> developers 테이블

| 컬럼명 | 설명 |
|------|------|
| id | 개발자 ID |
| first_name | 이름 |
| last_name | 성 |
|email | 이메일 | 
|skill_code| 개발자의 스킬 코드(정수형) |

> skillcodes

| 컬럼명 | 설명 |
|------|------|
| name | 스킬의 이름 |
| category | 스킬의 범주 |
| code | 스킬 코드(정수) |
---
### 접근 방식 1
1. 출력 결과물은 developers 테이블에서만 사용하므로 from절에 developers 테이블 작성
2. 스킬 코드는 이진 코드로 표현시 각 비트가 스킬 여부를 표현함을 인지
3. 개발자의 스킬 코드와 해당 기술의 스킬 코드를 & 연산자를 이용해 비교하여 스킬 보유 여부 확인 가능
5. 비교를 위한 해당 기술의 스킬 코드는 서브 쿼리를 이용해 skillcodes 테이블에서 가져 올 수 있음
6. WHERE 조건식 필터링을 이용해 해당 스킬 보유 여부에 따라 필터링
7. 개발자 정보 관련 칼럼 출력
8. 개발자의 ID 칼럼 기준으로 오름차순 정렬
---

### 시행착오
- 개발자 스킬 코드와 해당 스킬 코드의 값이 다름에도 스킬 코드 칼럼 기준으로 &연산자 이용해 테이블 조인
  <br>시도하였으나 문제 풀이 당시 **조인 기준을 정확히 명시하지 못함**
  <br> [& 연산자를 통한 값이 0이 아닌 기준을 통해 조인 후 해당 기술 이름으로 필터링 하여 풀 수 있음]
  
---

### 최종 쿼리
```sql
SELECT  ID,
        EMAIL,
        FIRST_NAME,
        LAST_NAME
FROM DEVELOPERS
WHERE SKILL_CODE & (SELECT CODE FROM SKILLCODES WHERE NAME = 'C#') > 0
    OR SKILL_CODE & (SELECT CODE FROM SKILLCODES WHERE NAME = 'Python') > 0
ORDER BY ID ASC;
```
---

### 접근방식 2 (조인을 통한 풀이)
1. 개발자 스킬 코드에 해당 스킬 코드가 포함되는 기준으로 나타내기 위해 &연산자를 이용해 테이블 조인
2. 조인한 테이블은 개발자 정보와 개발자가 보유한 스킬에 대한 정보를 나타냄. 개발자가 여러 스킬 보유 시 개발자 정보는 중복행 발생
3. WHERE 조건 필터링을 이용해 'C#', 'Python' 스킬 이름을 가진 행만 필터링
4. 한 개발자가 'C#', 'Python'스킬을 동시에 보유한 경우 중복행 발생하므로 DISTINCT 사용
5. 개발자 id 기준으로 정렬

---
### 시행착오
- 조인시 스킬을 동시에 보유한 경우 개발자 정보는 중복행이 발생함을 고려하지 못함
---

### 최종쿼리
```sql
SELECT DISTINCT d.ID,
       d.EMAIL,
       d.FIRST_NAME,
       d.LAST_NAME
FROM DEVELOPERS d
JOIN SKILLCODES s
  ON (d.SKILL_CODE & s.CODE) > 0
WHERE s.NAME IN ('C#', 'Python')
ORDER BY d.ID ASC;
```
