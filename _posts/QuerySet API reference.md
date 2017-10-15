# QuerySet API reference

### 쿼리셋이 평가되는 경우

- 반복시킬 때
- 슬라이싱. 'step'이 포함된 경우
- 피클링/캐싱
- repr() , len(), list(), bool()



### QuerySet methods

- filter (kwargs 사용, 복잡한 조건을 붙이고 싶다면 Q object 사용)
- exclude
- annotate (QuerySet 반환받을때 특정 정보를 제공해주는 주석을 달고 싶을 때)
- order_by 안해주면 기본값은 model클래스의 meta 섭클래스에 정의된 순서대로 정렬.
  -  다른 모델과 연관된 필드를 기준으로 삼으면 연관된 모델의 pk값을 기준으로 정렬(바꾸고 싶으면 역시 Meta클래스에서 재정의))
  - related 모델이 잇는 경우에는 **예상못한** 쿼리셋이 나올 수 있는 점 염두!
- reverse 오더링을 반대로 한다. 가장 마지막 쿼리셋 뽑을때 사용하면 유용
- distinct()
- values() 필드와 값들이 딕셔너리 형태로 포함된 쿼리셋 제공
  - 인자로 필드명을 넣으면 해당 인자만 반환
- values_list 이름은 리스트인데, 딕셔너리 대신에 튜플 반환. values와 비슷
  - values와 values_list는 모델 인스턴스를 만드는 부담 없이 해당 필드의 정보를 꺼낼 때 사용
- dates(인자 두개 받는다. 날자관련 field, 기준(년/월/일) distinct적용된 값이 쿼리셋으로 출력)
  - order 옵션 넣어서 ASC냐 DESC냐 정할 수 있음
- union, intersection, difference >> 두 개의 쿼리셋을 받아서 새로운 쿼리셋 뽑아낸다.
- select_related 데이터베이스 방문할 때 인자로 받은 연관된 모델까지 들른다. 때문에 이 때 반환된 쿼리셋을 효율적으로 사용해서 데이터베이스 방문 횟수를 줄일 수 있다.(self, OTO만 지원)
- prefetch_related MTM에 적용 가능. 기능은 select_related와 비슷
  - Prefetch('field', queryset=blah, to_attr=blah) 를 사용해서 추가적인 조정 가능
  - prefetch_related('pizzas_toppings', 'pizzas')와 같은 것은 두 번째 인자가 중복됨을 유의
- extra() 장고 기능이 충분하지 않을때 사용 SQL 문법에 가까운 수식을 포함한다.
- defer(), only() 는 특정 필드만 추출할 때 사용. defer()는 db가 복잡하면 잘 분석하고 해야한다.



### QuerySet이 반환되지 않는 메소드

- get, create, get_or_create, update_or_create
- bulk_create 한방에 여러개 만들때. save() 메소드 호출안됨
- count
- is_bulk pk값과 해당 오브젝트를 딕셔너리 형태로 출력
- latest 가장 최근 하나만 출력(기본값은 생성순서, fieldname을 넣어서 조정 가능) <> earliest
- exists() 존재여부 확인
- update db 수정기능. blog__name 과 같은 식으로 연결된 모델의 filed수정은 불가
- delete() 삭제
- field lookup (필드명 뒤에 __를 붙이고 사용한다)
  - exact, iexact(case-insensitive)
  - contains
  - in
  - gt , gt, lt, lte
  - startswith, istartswith, endswith, iendswith
  - range(날자 구간 정할때 사용)
  - date, year, month, day, week 등등 시간관련 조건은 그때그때 확인하자
  - **search** contains와 비슷한데 훨씬 빠르다고 한다.
  - regex 정규표현식 사용가능