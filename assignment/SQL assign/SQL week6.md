```
5-1 intro

다량의 자료를 연결 : join
여러가지 데이터를 참고하는 것

대부분은 다량의 자료, 그렇기에 조인 필수
```
```
5-2  join 이해하기

서로 다른 데이터 테이블을 연결하는 것!

ex) 포켓몬 데이터
두 데이터를 연결할 수 있는 공통 값이 있나?
trainer_id 와 pokemon_id 가 공통으로 들어간 칼럼
    -> 트레이너 데이터와 포켓몬 데이터를 조인시킬 매개체
    -> trainer_id를 기준으로 두 테이블을 결합

보통 id값을 Key로 사용, 특정범위도 가능

조인은 중복된 데이터를 최소화함
```
```
5-3 다양한 JOIN 방법 (LEFT, RIGHT, INNER, CROSS, JOIN)

SQL join 방법

    inner join : 두 테이블의 공통 요소만 연결
    left/right join : 왼쪽/오른쪽 테이블 기준으로 연결
    full outer join : 양쪽 기준으로 연결
    cross join : 두 테이블의 각각의 요소를 곱하기

left join만 잘 사용하자!

```
![설명 텍스트](./img/11100251.png)
![설명 텍스트](./img/11100252.png)