```
2-6. 연습 문제 1~3번

1. 포켓몬 중에 type2가 없는 포켓몬의 수를 작성하는 쿼리를 작성해주세요
```
![설명 텍스트](./img/09301346.png)
![설명 텍스트](./img/09301349.png)
```
-> 조건을 만족하는 값의 개수 (필으 이름이 cnt)
```
![설명 텍스트](./img/09301353.png)
```
-> type2가 null이면서, type1이 'Fire' (AND 연습)
```
![설명 텍스트](./img/09301359.png)
```
-> type2가 null이거나 type1이 'Fire' (OR 연습)
```
```
2. type2가 없는 포켓몬의 type1의 포켓몬 수를 알려주는 쿼리를 작성해 주세요. 단, type1의 포켓몬 수가 큰 순으로 정렬해 주세요
```
![설명 텍스트](./img/09301407.png)
```
-> select, from, where, group by, order by 순

-> type1과 count(id)를 불러옴
-> basic의 pokemon 테이블에서
-> 조건은 type2 is null
-> 행은 type1으로 그룹화
-> 내림차순 정렬
```
![설명 텍스트](./img/09301420.png)
```
-> distinct 사용 -> 중복값 제거
but 문제에서 id는 중복값이 없기에 distinct 사용에 따른 차이 없음
```
