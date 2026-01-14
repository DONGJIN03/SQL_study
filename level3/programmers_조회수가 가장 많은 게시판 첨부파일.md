## [조회수가 가장 많은 중고거래 게시판의 첨부파일 조회하기]

>핵심 개념: 서브쿼리, MAX(), CONCAT

### 문제 요약
  조회수가 가장 높은 중고거래 게시물에 대한 첨부파일 경로 조회
  
### 데이터 구조
> used_goods_board
| 컬럼명 | 설명 |
|------|------|
| board_id | 게시글 id |
| writer_id | 작성자 ID |
| title | 제목 |
| contents | 대여 종료일 |
| price | 가격 |
|created_date | 작성일 |
| status | 거래 상태 |
| views | 조회수 |


> used_goods_file
| 컬럼명 | 설명 |
|------|------|
| file_id | 파일 id |
| file_ext | 파일 확장자 |
| file_name | 파일 이름 |
| board_id | 게시글 아이디 |

---
### 접근 방식1 
1. 게실글에 대한 파일 정보를 알기 위해 게시글 아이디 기준으로 두 테이블을 조인한다.
2. 조회수가 최대값인 게시글을 조건식 필터링을 통해 찾을것이다. 이를 위해 서브 쿼리를 통해 게시판 테이블에서 조회수 최대값을 구한 뒤 조인한 테이블의 조회수와 같은 값을 찾는 필터링으로 최대 조회수 게시판의 파일 정보를 구한다.
3. 기본적인 파일경로는 /home/grep/src/ 이며, 게시글 ID를 기준으로 디렉토리가 구분되고, 파일이름은 파일 ID, 파일 이름, 파일 확장자로 구성되도록 출력해야 하므로 CONCAT 함수를 이용해 문자열을 이어붙여 출력해준다.
4. 파일 ID 기준으로 내림차순 정렬을 해준다.

---

### 시행착오
- 출력용 테이블을 서브쿼리를 이용해 사용했다. 이때 서브쿼리로 만든 테이블은 제시된 두 테이블을 조인한 후 조회수가 최댓값인 테이블을 사용할려고 하였으나 SELECT절에서 모든 정보와 MAX(views)를 출력하는 코드를 짰다. 해당 방식은 조회수 최댓값과 **모든 칼럼에 대한 임의의 정보가 출력 되므로 최대 조회수에 대한 다른 정보가 아니다**
- 위의 방식대로 풀기 위해 조회수 기준 내림차순 정렬 후 LIMIT을 이용해 첫번째 행만 출력하는 방식으로 조회수가 최대인 게시글과 파일 정보를 구할 수 있다. 문제에서 조회수가 최대인 게시글이 하나라고 알려주었지만 게시글에 대한 파일 이름명이 1개가 아닌 경우가 있으므로 적합하지 않다.
   
---

### 최종 쿼리
```sql
SELECT CONCAT('/home/grep/src/', f.board_id,'/',
              f.file_id, f.file_name, f.file_ext) as file_path
FROM used_goods_board b JOIN used_goods_file f
ON b.board_id = f.board_id
WHERE b.views = (SELECT MAX(views) FROM used_goods_board)
ORDER BY file_id DESC;   
```
