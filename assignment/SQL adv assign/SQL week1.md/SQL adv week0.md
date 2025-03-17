# SQL advanced week0
## 범위
15.2.15. Subqueries

15.2.15.2. Comparisons Using

15.2.15.3. Subquries with ANY, IN or SOME

15.2.15.4. Subqueries with ALL

15.2.15.6. Subqueries with EXIST or NOT EXISTS

15.2.15.10 Subquerey Errors

## 15.2.15.2. Subqueries
서브쿼리는 select 명령문 내의 명령문
```
SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
```
여기서 select * from t1 where column1 은 외부쿼리,
select column1 from t2 는 서브쿼리 (하위 쿼리)

## 15.2.15.2. Comparisons Using
comparison_operator : = < > <= >= <> != <=>
```
where 'a' = (select column1 from t1)
```
```
select * from t1
    where column1 = (select MAX(column2) from t2);
```
```
SELECT * FROM t1 AS t
  WHERE 2 = (SELECT COUNT(*) FROM t1 WHERE t1.id = t.id);
```

## 15.2.15.3. Subquries with ANY, IN or SOME
Any?

- 무조건 비교 연산자가 따라옴
- 서브쿼리에서 반환된 값 중 하나라도 조건을 만족하면 TRUE

```
SELECT s1
FROM t1 
WHERE s1 > 
    ANY (
        SELECT s1
        FROM t2
);
```
t1.s1이 t2.s1 값 중 하나보다 크면 해당 행을 반환

ANY와 ALL의 차이점?
- ANY는 하나라고 크면 참, ALL은 모두보다 크면 참

IN?

- = ANY와 동일하게 사용됨
- 동일한 값을 찾을 때만 사용되며, '<' '>' 등과는 사용불가
```
SELECT s1
FROM t1
WHERE s1 = ANY (
    SELECT s1
    FROM t2);

SELECT s1
FROM t1
WHERE s1 IN (SELECT s1
    FROM t2);
```
이 둘은 동일

SOME?

- ANY와 동일하게 동작, 잘 사용안됨

## 15.2.15.4. Subqueries with ALL
ALL 연산자는 뒤에 모든 값에 대해 TRUE이면 TRUE반환
```
SELECT s1
FROM t1
WHERE s1 > ALL (
    SELECT s1
    FROM t2);
```
t1의 s1의 값이 t2의 s1의 모든 값과 비교했을때 크면 그 행 가져옴

<br/>
결측치가 있을수도 있다!

결측치와 비교하면 잘못된 결과가 나올 수도 있기에,
```
SELECT *
FROM t1
WHERE 1 > ALL (
    SELECT MAX(s1)
    FROM t2);
```
이런식으로 대체 가능, 아니면
```
SELECT *
FROM t1
WHERE 1 > ALL (
    SELECT MAX(s1)
    FROM t2
    WHERE s1 IS NOT NULL);
```
요렇게

'NOT IN'과 '<>'
```
SELECT s1 FROM t1 WHERE s1 <> ALL (SELECT s1 FROM t2);
SELECT s1 FROM t1 WHERE s1 NOT IN (SELECT s1 FROM t2);
```
이 두 문장은 동일

테이블 키워드란?
<br/>

- SQL에서 특정 테이블의 모든 행을 반환할 때 사용
- Select * From 테이블명 과 동일한 의미

## 15.2.15.6. Subqueries with EXIST or NOT EXISTS
EXIST란?

- 서브쿼리의 결과가 존재하는지 확인할 때 사용
- 서브쿼리에 행이 하나라도 있으면 TRUE, 없으면 FALSE

```
SELECT column1
FROM t1
WHERE EXISTS (SELECT * FROM t2);
```
(SELECT * FROM t2);가 존재하기에, 모든 값 반환

```
SELECT DISTINCT store_type
FROM stores
WHERE EXISTS (SELECT *
                FROM cities_stores
                WHERE 
                    cities_stores.store_type = stores.store_type);
```
cities_stores.store_type과 stores.store_type이 일치하는 행이 존재하면 TRUE,
없으면 FALSE
-> TRUE면 WHERE절 충족

```
SELECT DISTINCT store_type
FROM stores
WHERE NOT EXISTS (SELECT *
                  FROM cities_stores
                  WHERE
                    cities_stores.store_type = stores.store_type);
```
EXIST의 반대, 모두 존재하지 않으면 TRUE