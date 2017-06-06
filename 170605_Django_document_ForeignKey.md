# Django_model
## ForeignKey
### ForeignKey(othermodel, on_delete,**options)

* **Many-to-one** ralationship 에서 위치인자를 필요로 한다.

```
class Car(models, Model):
	manufacturer = models.ForeignKey(
		'Manufacturer',
		on_delete=models.CASCADE,
	)

class Manufacturer(models.Model):
	pass	
	
```
> 위의 Manufacturer 의 클래스 선언이 Car의 위치보다 뒤늦게 되었기 때문에 Car 클래스에서 ' ' 로 감싸 표현한다.(아직 정의되지 않은 모델을 사용)   
> 재귀관계로 만들기 위해 othermodel 에 'self'를 사용한다.

* **Questino  (의미 이해 안돼)**  
	Relationships defiend this way on abstract models are resloved when the model is subclassed as a concrete modle and are not relative to the abstract model's app_label:
	
Manufacturer 의 상위에 Production이 있다면 다음과 같이 사용할 수 있을 것이다.

```
class Car(models.Model):
	manufacturer = model.ForeignKey(
		'poduction.Manufacturer',
		on_delete=models.CASCADE,
	)
```

### Arguments

* CASCADE = Django는 ondelete cascade를 포함한 SQL의 동작을 emulate하고 ForeignKey가 포함된 객체를 삭제한다.
* PROTECT = ProtectedError를 발생시켜 참조 객체의 삭제를 보호한다. 
* SET_NULL = Null is True 일때만 가능하다.
* SET_DEFAULT = ForeignKey를 기본값으로 설정한다.
* SET() = ForeignKey 를 SET()에 전달된 값으로 설정하거나 호출 가능 객체가 전달 된 경우 호출한 결과이다. models.py를 import시킬때 쿼리를 실행하지 않으려면 호출 가능을 전달해야 한다.**(의미를 잘모르겠다)**
* Do_NOTHING = 데이터베이스 벡엔드가 참조무결성을 적용에 있어 SQL ON DELETE 제약 조건을 포함하지 않는다면 IntergrityError 를 발생한다. 

* ForeignKey.limit\_choices\_to = 선택 항목의 제한을 설정한다. dict 나 Q 객체 , returning dict 에 사용

	```
	staff_n=member = models.ForeignKey(
		user,
		on_delete=models.CASCADE,
		limit_choices_to={'is_staff':True,
		}
	)	
	```
> 이를 통해서 modelForm의 필드가 is_staff=True 인 경우로 제한된다.

```
def limit_pub_date_choices():
	return {'pub_date__lte': datetime.date.utcnow()}
	
limit_choices_to = limit_pub_date_choices	
```
파이썬의 datetime 모듈과 함께 사용할 수도 있다.


* ForeignKey.related\_name = 관련 객체에서 사용할 이름이며 related\_query\_name 의 기본값이다. Django에서 backwards relation 을 원하지 않는다면 related_name 뒤에 '+' 로 끝내면 backwards relation 을 갖지 않는다.

```
user = models.ForeignKey(
	User,
	on_delete=models.CASCADE,
	related_name='+',
)	
```

* ForeignKey.related\_query\_name = reverse filter name 에 사용할 이름이다. related\_name or default\_related\_name 이 기본값이고, 그렇지 않다면 model의 이름이 기본이다.

```
class Tag(models.Model):
	article = models.ForeignKey(
		Article,
		on_delete=models,CASCADE,
		related_name='tags',
		related_query_name='tag',
	)
	name = models.CharField(max_length=255)
	
Article.objects.filter(tag__name='important')	
```
> related_name 을 tags 로 
> relate_query_name 을 tag 로 이용할 수 있다. 
> 이는 최하단의 Article.objects.filter(tag__name='important') 로 알 수 있다. tag를 사용하여 backwards relation 을 나타낸다.

* ForeignKey.to\_field = Django 는 관련 객체의 PrimaryKey를 사용한다. 다른 필드를 참조할 경우 unique = True 여야만 한다.

* ForeignKey.db\_constraint = ForeignKey 에 대해 데이터베이스 제약조건을 만들 것인가 제어. default 값은 True이고 False로 설정하면 데이터 무결성이 나빠질 수 있다. (that's almost certainly what you wnat; setting this to False cna be very bad for data integrity... **이해 안돼**)

* ForeignKey.swappable = 기본값은 True 이고 ForeignKey가 settings.AUTH_USER_MODEL의 현재값과 일치하는 모델을 가리키며 관계는 모델이 아닌 설정에 대한 참조를 사용하여 migration에 저장한다.

## ManyToManyField
