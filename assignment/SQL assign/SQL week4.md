```
3-4 오류를 디버깅하는 방법

많은 오류들 어떻게 해결?
```
```
Syntax error : 문법 오류
번역 -> 검색 (구글, 공식문서, GPT)

select list must not be empty at []
-> select목록은 비어있으면 안됨
```
```
number of arguments does not match for aggregate function count
-> 집계함수 count의 인자 수가 일치하지 않음
```
```
select list expression references column type1 a which is neither grouped not aggreagated
-> select 목록 식은 다음에서 그룹화되거나 집계되지 않은 열을 참조함
-> group by에 적절한 칼럼을 명시하지 않았을 경우
```
```
expected end of input but got keyword select
-> 입력이 끝날 것으로 예상했으나, select 키워드가 입력됨
-> 하나의 쿼리엔 select가 하나만 있어야함
혹은 쿼리가 끝나는 부분에 ; 붙이고 실행할 부분만 드래그해서 실행 
```
```
expected end of input but got keyword WHERE at []
-> 입력이 끝날 것으로 예상되었지만 []에서 키워드 WHERE을 얻음
-> limit 옮기거나 삭제
```
```
expected ")" but got end of script at []
-> ) 가 예상되지만 []에서 스크립트 끝남
```