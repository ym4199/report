# Django_document
## Making queries

### create objects

객체를 생성하는데 있어 방법은 두가지로 정리할 수 있다.   

1. instance를 생성하고 이를 save한다.

	```
	b=Blog(name='Beatles Blog', tagline = 'all the latest beatles news.'
	b.save()
	```


2. 생성 단계부터 create로 접근하면 save 명령이 필요하지 않은 채 생성 및 저장된다.

	```
	b=Blog.objects.create(
		name='Beatles Blog', 
		tagline = 'All the latest Beatles news.'
	)
	```
	
### ForeignKey & ManyToManyField

> ForeignKey 와 ManyToManyField 가 선언된 경우.  
각 모듈에 대해 인스턴스를 생성하고 이를 한쪽에 추가하는 개념으로 받아들이자.

```
john = Author.objects.create(name='John')
paul = Author.objects.create(name='paul')

entry.authors.add(john, paul)
```

앞의 기준에 뒤의 add 이하를 더해준다.  
즉, entry 에 john 과 paul이라는 author를 더해주는 것이다.  
반대의 개념으로도 추가 가능하다.

```
john.entry_set.add(entry)
```

이때 author_set 은 author에 대한 entry를 역참조 하는 것이다.


### Retrieving all objects

테이블로부터 포함되는 모든 객체를 받을때 사용한다. all() 을 사용하여 받을 경우 return 은 **QuerySet** 임을 잊지말자.

```
all_entries = Entry.objects.all()
```

### Retrieving specific objects with filters

* filter(\*\*kwargs) : 조건에 맞는 객체를 QuerySet으로 반환한다. 

* exclude(\*\*kwargs) : 조건에 부합하는 객체를 제외한 나머지 객체로 QuerySet 을 반환한다.

filter의 경우 다음과 같이 사용하면 동일한 결과를 얻게된다.

```
Entry.objects.filter(pub_date__year=2006)

Entry.objects.all().filter(pub_date__year = 2006)
```

### Retrieving a single object with get()

filter() 의 경우 조건에 맞는 객체를 통해 QuerySet 으로 반환해주는데  
get() 의 경우 하나의 query(요소)로 반환된다.

### Limiting QuerySets

QuerySet은 SQL의 일부를 사용할 수 있다. 대표적으로 slice를 볼 수 있다.

```
Entry.objects.all()[:5]
Entry.objects.all()[5:10]
```

### Field lookup

던더(더블 언더)를 통해 세부 옵션사항을 설정할 수 있다.

```
Entry.objects.get(headline__exact="cat bites dog")
```

### Complex lookups with Q objects

filter 내의 keyword argument queries 에서 복합적인 조건을 사용할 수 있게 만들어준다.

```
Q(question__startwith='who')|Q(question__startswith='what')
```

```
Poll.objects.get(
	Q(question__startswith='who'),
	Q(pub_date=date(2005,5,2)|Q(pub_date=date(2005,5,6))
)
```