# SQL advanced week4
## 범위
15.2.20 WITH (Common Table Expressions)

## 15.2.20 WITH (Common Table Expressions)

- CTE, GROUP_CONCAT(), WITH_RECURSIVE

### CTE
WITH 절로 정의하는 일시적인 이름 있는 서브쿼리(?)

재사용 가능, 쿼리 가독성 향상에 효과적

```SQL
WITH cte_name AS (
  SELECT ...
)
SELECT * FROM cte_name;
```
사용위치 : SELECT, UPDATE, DELETE, INSERT 문 맨 앞, 서브쿼리 안

### GROUP_CONCAT()
여러 행의 문자열 값을 하나로 합쳐서 반환

계층 구조에서 경로 생성에 유용

```SQL
SELECT GROUP_CONCAT(name) AS 이름모음
FROM (  SELECT '철수' AS name
        UNION
        SELECT '영희'
        UNION
        SELECT '민수')
        AS t;
```
-> 철희,영희,민수

### WITH_RECURSIVE
CTE 중 하나가 자기 자신을 참조하는 경우

계층 구조, 연속된 시퀀스 생성, 트리 탐색 등에 사용

```SQL
WITH RECURSIVE cte_name (col1, col2, ...) AS (
  SELECT ...        -- 초기값 (non-recursive part)
  UNION ALL
  SELECT ... FROM cte_name WHERE ... -- 재귀적 호출 (recursive part)
)
SELECT * FROM cte_name;
```
UNION ALL로 이어진 두 SELECT -> 처음은 기준값, 두 번째는 반복적 확장

종료 조건을 WHERE 절에 반드시 포함해야 무한루프 방지

재귀 깊이는 cte_max_recursion_depth로 제한됨

## 문제1
```SQL
SELECT CART_ID
FROM CART_PRODUCTS
WHERE NAME IN ('Milk', 'Yogurt')
GROUP BY CART_ID
HAVING GROUP_CONCAT(DISTINCT NAME ORDER BY NAME) = 'Milk,Yogurt';
```