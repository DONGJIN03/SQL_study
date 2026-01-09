## [업그레이드 된 아이템 구하기]

>핵심 개념: JOIN, 서브쿼리

### 문제 요약
  아이템의 희귀도가 'rare'인 아이템들의 모든 다음 업그레이드 아이템의 아이디, 아이템 명, 희귀도를 출력

### 데이터 구조
> item_info 테이블

| 컬럼명 | 설명 |
|------|------|
| item_id | 아이템 ID |
| item_name | 아이템 명 |
| price | 아이템 가격 |

> item_tree

| 컬럼명 | 설명 |
|------|------|
| item_id | 아이템 ID |
| parent_item_id | Parent 아이템 ID |
---
### 접근 방식
1. 부모 아이템(업그레이드 이전 아이템)의 희귀도가 'rare' 인 경우 item_info 테이블 정보 출력하는 문제임을 인식
2. 해당 아이템의 부모 아이템과 아이템 정보를 함께 나타내기 위해 item_info 테이블의 item_id 칼럼과 item_tree 테이블의 item_id 칼럼 기준으로 조인
3. WHERE 조건식에서 부모 아이템의 희귀도가 rare인 경우를 필터링 하기위해 부모 아이템 희귀도 정보가 필요함
4. 부모 아이템 id 역시 아이템 id이므로 서브쿼리를 이용해 희귀도가 'rare'인 아이템 id 추출
5. 희귀도가 'rare'인 아이템 id와 같은 id를 가진 부모 아이템 값만 조건 필터링
6. item_info 테이블의 정보들 출력
7. 아이템 id 기준으로 내림차순 정렬 
---

### 시행착오
- 부모 아이템 희귀도가 'rare'인 아이템을 찾고자 item_info의 아이템 아이디와 item_tree의 부모 아이템 아이디가 동일한 기준으로 조인을 하여 조건 필터링시 부모 아이템의 정보를 나타내는 테이블이 남음. (업그레이드 이후 아이템 정보를 출력해야 함)
  <br> **출력할 테이블과 서브쿼리로 사용할 테이블을 구분해서 구상할 것**

---

### 최종 쿼리
```sql
SELECT  I.ITEM_ID,
        I.ITEM_NAME,
        I.RARITY
FROM ITEM_INFO I JOIN ITEM_TREE T
ON I.ITEM_ID = T.ITEM_ID
WHERE T.PARENT_ITEM_ID IN (SELECT ITEM_ID FROM ITEM_INFO WHERE RARITY = 'RARE')
ORDER BY I.ITEM_ID DESC;
