# Django_Document
## Models
* 필수적인 필드와 데이터 동작에 대한 정보를 포함한다. 일반적으로 단일 데이터베이스 테이블에 각각이 매핑된다.

> Django 자동생성된 데이터베이스 액세스 API를 제공한다.  
> Django.db.models.Model의 하위클래스인 파이썬 클래스이다.  


##### model 의 사용
* model을 정의하면 Django에서 사용할 수 있도록 만들어줘야한다. settings > INSTALLED_APPS 에 모듈을 추가시켜주는 작업을 한다.


### FIELDS
* 가장 중요한 부분은 모델의 **데이터베이스 필드의 리스트**를 정의하는 것이다. 필드는 클래스의 속성에 따라 정해진다. 이때 필드 네임과 models api 내의 clean, save, delete 등이 충돌하지 않도록 주의해야 한다.

##### Field types
* 각 필드는 모델의 적절한 필드 클래스의 인스턴스가 되어야 한다. 

> column type : INTEGER, VARCHAR, TEXT : 데이터베이스에 저장할 종류 나타낸다.  
> 양식필드를 렌더링 할때 기본 HTML 위젯을 사용한다.  
> 최소한의 유효확인 요구사항에 Django's admin 과 자동생성 양식 양식에 사용된다.

##### Field options
각 필드는 필드에만 사용할 수 있는 옵션이 있다.

> CharField 는 필수적으로 max_length 옵션이 주어져야 한다. 그래야만 실제 db에 VARCHAR 컬럼을 만들때 길이를 초기화 할 수 있기 때문이다.
  
> null 은 만약 True 라면 저장공간을 null처리한다. 기본적으로 False 값이다.

> black 은 만약 True 라면 필드는 빈공간을 허락한다. 기본적으로 False 값이다.
 
* **null 값과 blank 값은 같아 보이지만 차이가 있다. blank 는 빈 공간을 차지하지만 null은 값 자체가 없다는 것이다.   
null 은 데이터베이스에만 관련되어 있고 blank 는 유효성 검사와 관련있다. blank = True 가 있으면 양식 유효검사에서 빈값을 입력할 수 있다.  
blank = False 면 필드가 필요해진다.**

> Choices 는 반복가능한 대상(튜플)이 주어지면 기본 양식 위젯은 선택상자를 표준 텍스트 필드 대신 주면서 선택을 제한한다.  
> get_필드명_display() 로 메서드를 호출하면 튜플의 두번째 요소 값을 얻을 수 있다.

##### default
*필드 기본값이며 호출 가능하면 객체가 만들어지는 매번 호출한다.

### primary_key
* True 라면 필드는 모델의 primary key이다.
* 어떤 필드에도 primary_key를 지정하지 않았다면 Django 는 자동으로 IntergerField를 자동으로 추가할 것아며 primary_key를 가질 것이다. 기본primary_key 동작을 override하고 싶지 않다면 primary_key = True 설정 할 필요가 없다. 
  
>기본적으로 Django는 별도로 지정하지 않는 경우, 'id' 라는 IntegerField 하나를 모델에 자동으로 추가하고, 그 필드에 'primary_key=True' 옵션을 설정한다. 자동으로 생성되지 않게 하기 위해서 원하는 필드에 primary_key=True 옵션을 주면 된다.

* primary_key 는 읽기전용이다. primary_key의 값을 존재하는 객체와 함께 변경하고 저장하면  이전 것과 함께 새로운 객체가 만들어진다.

> 기존의 모델 객체에 primary_key 필드값을 변경하고 저장하는 경우, 기존 모델 객체의 primary_key 필드값이 바뀌는 것이 아니라, 새로운 객체가 생성된다. 이때 기존의 모델객체는 db상에서 지워지지 않는다.

> Django는 pk 값을 기준으로 이미 존재하는 레코드이면 update를, pk값이 조재하지 않는 경우 create를 수행한다.

* unique True 라면 필드값은 해당 테이블 전체에서 유일해야 한다.

### Automatic primary key fields
* Django 는 모델에 

```
id=models.AutoField(primary_key=True
```
필드를 자동으로 생성한다.  
Django 모델은 필수적으로 하나의 primary_key를 가져야한다.

### Verbose field names
* ForeignKey, ManyToManyField, OneToOneField 을 제외한 각 필드는 첫번째은 첫번째 자리의 인자로 verbose name 을 취한다. verbose name 이 주어지지 않았다면 Django는 자동으로 속성값으로 이름을 정한다. 
* ForeignKey, ManyToManyField, OneToOneField 는 첫번째 인자를 모델 클래스로 받기 때문에 verbose name을 지정하기 위해 키워드 인자로 사용해야 한다.


## Relationships
### Many-to-one relationships
* django.db.models.ForeignKey 를 이용하여 정의한다. ForeignKey 는 위치 인자를 필요로 한다. 
* car라는 모델은 manufacturer를 가지고 있고 다양한 car를 manufacturer가 만들지만 각 car는 하나의 manufacturer만을 갖는다.

> recursive relationships(재귀적관계)   
> 첫번째 인자를 클래스가 아닌 클래스 명을 문자열로 전달해야 합니다. 필드가 선언되는 시점에는 아직 클래스가 생성되지 않았기 때문입니다. 즉, 클래스 대신 클래스 명을 인자로 사용함으로써 클래스 선언 순서에 관계없이 참조가 가능합니다.

### many-to-many relationships
* ManyToManyField 를 사용하여 다대다를 정의한다. 다른 필드타입처럼 모델 클래스의 속성을 포함한다.
* 모델과 관계를 갖는 클래스를 위치 인자로 필요로 한다.
* 피자는 다양한 토핑이 있는데 토핑은 다양한 피자에 올라갈 수 있다. 즉, 각 피자는 다양한 토핑을 갖는다.

> ForeignKey 처럼 재귀적관계를 갖을 수 있다. ManyToManyField 의 필드이름은 복수로 적어주는 것을 추천한다.  
> ManyToManyField 는 관계 갖는 한쪽에만 선언한다. 이때 위치는 상관은 없다. 편집하기 편한쪽으로 선택하자.


### Extra fields on many-to-many relationships
* 두 모델들 사이 관계가 추가적인 데이터를 갖는 경우가 있다.   

> 음악가 속한 음악 그룹을 추적하는 응용프로그램을 생각해보자. 개인과 그룹 간에 다대다 관계가 있고 ManyToManyField를 사용할 수 있다.  
> 그룹에 가입한 날짜와 같은 자세한 정보를 수집하는데 이런 환경에서 다대다 관계를 사용할 수 있다. 
> 중간 모델에 추가 필드를 놓을 수 있다. 중간 모델은 ManyToManyField와 연관있고 through 인자를 사용하여 모델을 지목하는 중개자 역할을 한다. 

* 중간모델은 **반드시 하나**의 Foreignkey를 가져야 한다. 혹은 ManyToManyField.through_fields 옵션으로 관계에 사용되는 필드 이름을 정해줘야 한다.
* 둘 중 하나가 아니라면 Validation 에러가 발생한다. 
* 스스로에게 다대다 관계를 가지는 모델의 경우 중간 모델에 동일한 모델에 대한 ForeignKey 가 두개 허용될 수 있다.  
이때, ManyToMany필드의 symmetrical 옵션을 False로 설정해야 한다.

> many-to-many 필드에서 add(), create(), set() 은 사용할 수 없다. 
> 두 클래스의 관계를 설정할 때 중간 모델의 필드값의 세부사항을 부여해야하기 때문이다.   
>  add 나 create 는 중간모델의 person 이나 group 의 필드값은 알 수 있지만, date_joined 혹은 invite_reason 같은 세부 사항의 필드값은 알 수가 없기 때문이다.  
> 중간 모델의 인스턴스를 새로 만드는 것이 해결 방법이다.  
>  remove() 메서드도 사용할 수 없으나 clear() 메서드는 사용할 수 있다. clear()를 이용하여 인스턴스의 모든 many-to-many 관계를 제거할 수 있다.

* 관계를 생성할때는 제약이 있지만(add,remove 등의 사용불가) 쿼리이슈에서는  many-to-many relationships 와 동일하게 사용할 수 있다.

### One-to-one relationships
* 일대일 관계를 정의하기 위해, OneToOneField를 사용한다. 다른 필드와 마찬가지로 모델 클래스의 속성을 포함한다.
* 다른 객체에 확장할때 가장 유용한 primary_key이다.
* OneToOneField는 관계를 맺는 모델 클래스를 위치 인자로 받는다.


### Models across files
다른 app과 관계된 모델을 가져올 수 있다. 상위에 어디서 모델이 정의 되었는지 import시켜서 다른 곳에서 충분히 사용가능하다.

```
from django.db import models
from geography.models import Zipcode

class Restaurant(models.Model):
	zip_code = models.ForeignKey(
	ZipCode,
	on_delete=models.SET_NULL,
	blank=True,
	null=True,
)
```

위와 같이 상단에 models 와 Zipcode	를 import 시켜주었다. 이로써 해당 위치에서 다른 app 에 관계된 모델을 사용가능해졌다.


### Field name restrictions
Django 에서 이름을 지을때 주의점이 두가지있다.

1. 필드 이름은 파이썬에서 미리 정의된 이름을 피할 것.

	> 파이썬에 미리 정의된 이름과 충돌이 발생하여 원하는 동작이 불가능해진다.  

2. 필드 이름에 사용시 던더는 사용하지 말자.   
  즉, 언더스코어는 하나만 사용하자. 

	> 던더는 함수의 세부사항을 나타낸다. 따라서 단순히 작명에 있어서 던더를 사용하게 될 경우 내부 속성 값으로 장고는 착각할 수 있다.


### Meta Options
* 메타데이타는 필드에 추가 시키고 싶지 않지만 필드 옵션을 위해 필요한 데이터를 정의할 수 있다. 가령 데이터 테이블의 이름이나 정렬 옵션 같은 것들을 말할 수 있다.


### Model attributes
* 사실 모델의 attribute 중 가장 중요한 것은 Manager 이다. 데이터 베이스를 검색하고 쿼리 옵션들을 제공해준다. 앞서 사용하고 있던 objects 도 Manager에 속한다.

### Model methods
* custom methods 를 사용하여 row-level 기능을 객체에 추가한다.
* Manager methods 은 table-wide 를 수행한다.
* model methods 는 특정 model instance 에서 작동한다.

```
from django.db import models

class Person(models.Model):
	firtst_name = models.CharField(max_length=50)
	last_name = models.CharField(max_lenght=50)
	birth_date=models.DateField()
	
	def baby_boomer_status(self):
		"returns the person's baby-boomer status."
		import datetime
		if self.birth_date < datetime.date(1945,8,1):
			return "pre-boomer"
		elif self.birth_date < datetime.date(1965,1,1):
			return "baby boomer"
		else:
			return "post0boomer"
		
	@property
	def full_name(self):
		"return the person's full name."
		return '%s %s' % (self.first_name, self.last_name)	
```

@property 라고 표시된 decorator 가 위에서 나타낸 model methods	이다.


* \_\_str__()   
	python 에서 "magic method" 라 부르는데 모든 객체의 유니코드를 표현으로 돌려준다.

* get\_absolute\_url()	
	객체의 url들을 더하는 방법을 알려준다.
	

### Overriding predefined model methods
* model method 의 다른 예이다. 
* save() 나 delete() 의 행위를 사용자에게 맞춰 사용한다.

```
from django.db import models

class Blog(models.Model):
	name = models.CharField(max_length=100)
	tagline = models.TextField()
	
	def save(self, *args, **kwargs):
		if self.name == "yoko Ono's blog":
			reutnr 
		else:
			super(Blog, self).save(*args, **kwargs)
```

* super 라는 클래스 메소드를 호출하는 것이 중요하다. super 클래스를 호출하지 않는다면 default 가 발생하지 않을 것이고 데이터베이스를 건드리지 않는다.
* 또한 \*args, \*\*kwargs 를 사용하는 것이 매우 중요하다. Django는 실시간으로 새로운 인자를 추가하는데 이럴때 추가로 인수를 지정할 필요가 없어지기 때문이다.

### Model inheritance
* 상속의 개념은 python 내에서 사용하던 것과 비슷하게 사용된다. 그러나 페이지 시작부에 여전히 import를 나타내야한다. 이는 기본 클래스가 django.db.models.Model 의 하위개념임을 말해준다.

	1. 부모 클래스의 정보를 갖고 자식 클래스에 기입하지 않더라도 자동으로 갖도록 하고 싶으면 Abstract base classes 를 사용해라.
	
	2. 다중 테이블 상속으로 이미 존재하는 모델과 각각의 모델이 갖는 데이터 테이블을 하위 클래스화 할수 있다.
	
	3. Proxy models 를 사용하여 python 수준의 작동만 수정할 수 있다.

### Abstract base classes
* Abstract base classes(추상클래스)는 공통의 정보를 다수의 다른 모델에 부여하고자 할때 유용하다.

```
from django.db import models

class CommonInfo(models.Model):
	name-models.CharField(max_length=100)
	age = models.PositiveIntegerField()
	
		class Meta:
			abstract = True
	class Student(CommonInfo):
		nome_group = models.CharField(max_length=5)
```

#### meta inheritance
자식 클래스가 meta class 를 선언하지 않는다면 부모의 meta class 를 자동으로 상속받는다.기본적으로 abstract=False 값이 default 값이다.


### Multi-table inheritance
* Django 상속의 개념 두번째 타입이다. 각 모델이 하나의 데이터베이스 테이블에 해당할 때 사용할 수 있으며, 자동으로 생성된 일대일 필드에 링크를 함으로 가능하다.

#### meta and multi-table inheritance
* multi-table 상속에서 자식클래스가 부모의 meta클래스 상속받는건 의미 없다. 모든 메타 옵션은 이미 상위 클래스에 적용되어 있다. 이를 다시 적용하면 모순이다.
* parent_link = True 를 설정함으로써 필드가 부모 클래스의 링크임을 나타낼 수 있다.

### Proxy models
* 테이블을 가지는 모델을 상속받지만 자식모델은 테이블을 만들 필요가 없는 경우이다.
* meta 클래스를 proxy = True 를 선언함으로서 사용할 수 있다. 
* proxy 모델에서 변경한 사항은 부모 모델에 영향을 끼치지 않는다.
* proxy 모델은 하나의 모델만을 상속받는다. 반면 abstract 클래스 경우 추상모델 클래스가 필드를 가지지 않는다면 몇개든 상속이 가능하다.

> procy 모델은 반드시 추상모델이 아닌 모델을 상속해야 한다.