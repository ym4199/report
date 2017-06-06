# Django_Document
## Model field reference

### Field option

#### Null

* null = 이 값이 True 일 경우 Django 는 빈 값을 데이터베이스에 저장한다. 기본 값은 Default 값이다. 즉, 변경하지 않는다면 빈 값은 저장불가능하다. 

> Null의 사용을 피하기 위해 CharField and TextField 와 같은 string-based fields를 사용해야 한다. 

* string-baseed field가 null=True 일 경우 두가지를 의미한다.   
첫째, 데이터 자체가 없다는 뜻이며   
둘째, 빈 문자열 이라는 것이다.   
이 둘은 미묘하게 차이가 나는데 null 은 데이터의 존재가 없는 것이다. 빈 문자열은 데이터는 존재하지만 그 값이 비었다는 것을 의미한다.

> 대부분의 경우 빈값에 대해 null을 고려하지 않는다. CharField 에 unique = True 와 blank = True 가 설정된 경우 예외다. 빈 값으로 다수의 객체를 저장할 때 null = True 가 필요하다.

* BooleanField 에서 null 을 사용하고 싶을 경우, NullBooleanField를 사용한다.

#### Blank

* Field.blank =  기본 값은 False 이며  True 일 경우 빈칸을 허용한다.  

> 앞서 말한바와 같이 Null 값과는 차이를 지니기 때문에 주의하여 사용해야한다. 

#### choieces

* Field.choices = list or tuple 로 구성된 반복가능한 항목에서 사용가능하다. 텍스트 필드 대신 선택 상자 옵션으로 바뀐다.

```
YEAR_IN_SCHOOL_CHOICES = (
	('FR','Freshman'),
	('SO','Sophomore'),
	('JR','Junior'),
	('SR','Senior'),
```

튜플의 첫번째 요소는 모델에 설정할 실제 값이고, 두번째 요소는 사용자가 읽을 수 있는 값이다.  
모델 클래스 내에서 선택 사항을 정의하고 각 값에 대해 적절한 상수를 정의하는 것이 좋다.

```
class Student(models.Model):
	FRESHMAN = 'FR'
	SOPHOMORE = 'SP'
	JUNIOR = 'JR'
	SENIOR = 'SR'
		YEAR_IN_SCHOOL_CHOICES = (
		(FRESHMAN, 'Freshman'),
		(SOPHOMORE, 'Sophomore'),
		(JUNIOR,'Junior'),
		(SENIOR,'Senior'),
	)
	year_in_school = models.CharField(
		max_length=2,
		choices=YEAR_IN_SCHOOL_CHOICES,
		default=FRESHMAN,
	)
	
	def is_upperclass(self):
		return self.year_in_school in (self.JUNIOR, self.SENIOR)	
```

모델 클래스의 외부에서 참조하여 모델 클래스 내의 각 선택 항목에 대한 선택 사항과 이름을 정의하면 해당 정보를 사용하는 클래스와 모든 정보가 유지되고, 선택 사항을 쉽게 참조 할 수 있다. 


```
MEDIA_CHOICES = (
	('Audio',(
		('vinyl','vinyl'),
		('cd','CD'),
		)
	),
	('Video', (
		('vhs', 'VHS Tape'),
		('dvd', 'DVD'),
		)
	),
	('unknown', 'Unknown'),
)	
```

첫번째 요소는 각 튜플의 대한 이름이며 두번째 요소는 반복 가능한 두 튜플이고 각 튜플에 대한 값과 사용자가 읽을 수 있는 내용을 담은 옵션이다.  
 

blank=False 를 하지 않았다면 "-----" 가 포함된 라벨이 선택상자와 함께 렌더링 된다. 이런 상황을 막기 위해 choices에 튜플을 더할때 None 값을 포함해야 한다. (None, 'blahblah')

#### db_column
Field.db_column 은 데이터베이스 열의 이름으로 사용되기 위한 필드이다. 이 값이 없다면 Django는 필드의 이름으로 사용할 것이다.
데이터베이스 열 이름이 SQL reserved word 혹은 python 변수 이름이 아니면 가능하다. Django 는 열과 테이블 이름을 인용할 것이다.

#### db_tablespace
Field.db_tablespace 의 이름은 필드의 index 를 위해 사용된다. 기본 값은 프로젝트의 DEFAULT_INDEX_TABLESPACE.  
백엔드가 지원하지 않는다면 이 옵션은 무시된다.


#### default
Field.default 는 필드의 기본값을 위함이다. 호출가능하다면 호출할 때 마다 새로운 객체를 생성한다.

기본값은 변경 가능한 객체가 아니다.(list, set, model instance) 같은 인스턴스 객체를 참조함으로서 새 모델 인스턴스의 기본값으로 사용된다.

람다는 기본값 필드옵션으로 사용될 수 없다. 

#### editable
Field.editable 이 False 이면 필드는 admin 이나 다른 modelFrom상에 보여질 수 없다. 따라서 기본값은 True 이다.

#### error_messages
Field.error_messages 인수를 사용하면 필드에서 기본메세지 발생을 대체 할 수 있다. override 하고자 하는 메세지와 매칭하는(일치하는) 키 가있는 사전을 줘라.

> error_message 는 null, blamk, invalid, invalid_choice, unique, unique_for_date가 포함되어있다. 


#### help_text 
Field.help_text 는 추가 도움말 텍스트를 양식 위젯과 함께 보여준다. 이는 양식상에서 필드를 사용하지 않아도 유용하다.


#### primary_key
Field_primary_key 가 True 라면 모델을 위한 primary key가 필드이다.  
primary_key=True 가 필요 없다면 어떤 필드에서나 Django는 primarykey를 갖기 위해 자동으로 AutoField를 더할 것이다. 

* primary_key = True 는 null = False 및 unique = True 를 의미한다. 
* 단 하나의 primary key만 객체에 허용한다.
* primary_key 는 오직 읽기 전용이며 primaryKey를 변경한 값으로 바꾸면 이 전 값과 새로운 값이 속하게 된다.

#### unique
Field.unique 가 True 라면 테이블 전체에서 고유해야한다. 