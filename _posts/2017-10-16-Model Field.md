# 모델 필드를 알아보자

### 필드 옵션

null, blank, choices, db_column(기본값 안주면 필드 이름 그대로 사용), db_index(True로 설정하면 db에서 인덱싱을 계속 해줌)

default(불변객체만 가능, lambda 지원 안함, JSONField에서는 함수명으로 사용가능)

editable(False면 admin창에 안나옴), primary_key(_id 대신 해당 field를 pk로 사용, 당연히 null=False, unique=True로 값이 지정된다)

unique_for_date/month/year > 각각 일/월/년을 기준으로 기존과 같은 값을 가진 새로운 엔트리 차단

validators(새로운 엔트리에 조건에 안맞는 것이 있으면 db에 안넣게 할 수 있다.)

### 필드 타입

(Big)AutoField(pk 값이 있으니까 보통은 잘 안씀)

(Big)IntegerField, BooleanField(어디다 쓸까?), * NullBooleanField는 null값 지정 가능

CharField,  DateField(auto_now는 save, update때, auto_now_add는 create때)

DecimalField (소수점 단위수를 명확히 조정하고 싶을때 사용. max_digit과 decimal_places 사용)

DurationField(Create된 시점에서 얼마나 시간 지났는가)

EmailField (이메일전용)

FilePathField(특정 파일의 경로 알고싶을때 사용)

FloatField(소수점), IntegerField(정수)

GenericIPAddressField(ip주소)

SlugField(해당 테이블 row에서 라벨 역할 한다고 보면 될듯)

TextField(CharField unlimited)

URLField, UUIDField (pk값으로 활용 가능한것 같은데, 그냥 default값 안쓰는 경우가 뭐가 있을까?)



### FileField

파일 업로드 관련 필드.

**arguments**:

- upload_to =문자열 넣으면 MEDIA_ROOT 폴더 안쪽에 save해줌 (함수로 만들어서 사용도 가능)
- 객체는 storage 클래스를 통해서 관리 
  - FileSystemStorage(location='blah') 를 FileField의 아규먼트 안에 넣으면 임의로 파일 저장되는 장소 지정가능
- model에서 FileField객체에 접근하면 FieldFile 객체를 얻는다
  - 이를 통해 할 수 있는 것은
  - name, size, url
  - open, save << 이 둘은 언제사용?

### ImageField

FileField 상속받았으므로 비슷하게 사용하면 됨

아규먼트:

- height_field, width_field



### Relationship Fields

기본 아규먼트 2개(참조할 테이블, on_delete)

on_delete는:

- CASCADE 지우기
- PROTECT 보존
- SET_NULL NULL로 바꾸기
- SET_DEFAULT 설정값으로 바꾸기
- SET() 내가 설정한 함수 호출 가능
- DO_NOTHING

속성

- related_name 역참조할때 사용할 이름을 직접 지정. default값은 _set이다. 따로 설정 안해주면 related_query_name도 동일
- to_field : 기본값은 pk, 다르게 하고싶으면 unique한 값 골라야함
- swappable은 뭔지 전혀 모르겠다
- ManyToMany의 symmetrical ( **self**로 할 때만 사용한다)
- ManyToMany의 through
  - 추가적인 data가 있을 경우에 사용한다
  - 해당 through가 적용된 모델은 through가 사용된 모델, target 모델을 필드로 삼고 있어야한다.
- through_fields (ForeignKey가 2개 이상일때만 적용. 첫번째에는 해당 모델 필드명, 두번째는 타겟모델 필드명)

