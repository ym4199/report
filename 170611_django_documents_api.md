# QuerySet API reference

QuerySet 은 실제로 db에 도달하지 않고 구성, 필터링, 슬라이스, 일반적인 통과를 한다. 실제로 queryset 평가할때까지 데이터베이스 활동이 발생하지 않는다. 

* Iteration : QuerySet은 반복가능하다. 처음 반복할 때 QuerySet을 실행한다.


* slicing : queryset은 python 의 슬라이스를 사용할 수 있다. 평가된 QuerySet은 list를 반환한다. 
* repr() : QuerySet 은 repr()을 사용했을 때 평가된다. python 인터프리터에서 편리하다. API 사용할 때 결과를 즉시 볼 수 있기 때문이다.
* len() : QuerySet 은 len()을 사용했을 때 평가된다. python 처럼 길이를 반환한다.
* list() : list로 QuerySet을 강제로평가한다. 
```
entry_list = list(Entry.objects.all())
```
* bool() : QuerySet 을 boolean context(bool(), or, and 과 같은) 에서 테스트하면 query가 실행된다. 결과값을 True, False로 반환한다.

### Pickling QuerySets

QuerySet 을 pickle 하면 강제로 pickling 전의 메모리 결과를 로드한다. pickling 은 caching 전에 사용되며, cach된 queryset을 다시 로드할 때, 원하는 결과가 이미 준비되고 사용할 준비가 돼있다.  
즉, QuerySet 을 unpickli할 때, 현재 데이터 베이스의 결과가 아닌 쿼리 된 결과를 포함한다.

## QuerySet API

### QuerySet 의 선언

```
class QuerySet(model=None,query=None,using=None):

```

QuerySet 과 상호작용할 때 필터를 연결하여 사용한다. 이 작업을 수행하기 위해 QuerySet Method는 새로운 queryset들을 반환한다. 

* ordered : QuerySet이 순서가 맞으면 True 이다. 
* db : 쿼리가 실행될 경우 사용할 데이터베이스


## Methods that return new QuerySets

> Django 는 QuerySet 이 반환한 결과, sql 쿼리가 실행되는 방식을 수정하는 다양한 queryset 세분화 메소드를 제공한다.

* filter(**kwargs) :  지정된 매개변수와 일치하는 객체가 포함된 새로운 QuerySet을 반환한다.   
다양한 매개변수는 sql 문에서 처럼 and를 통해 연결된다.  
더 복잡한 쿼리는 Q objects를 사용하여 연결할 수 있다.

* exclude(**kwargs) : 매치되지 않는 값을 제외한 새로운 QuerySet을 반환한다.
* annotate(\*args, \*\*kwargs) : 키워드인자로 주석을 사용하면 해당 주석의 키워드로 사용된다. 
**잘 모르겠다**

> 반환되는 QuerySet의 객체에 추가되는 주석

```
>>> q = Blog.objects.annotate(Count('entry'))
>>> q[0].name # 어떤 값이 나오겠죠
>>> q[0].entry__count # entry에서 카운트한 숫자가 나온다.
```

* order_by(fields) : QuerySet  반환된 결과는 메타의 정렬 옵션에 의해 주어진 순서 튜플에 의해 정렬된다. order_by method 사용하여 QuerySet 단위로 겹쳐쓸 수 있다.

> order\_by() : 옵션사항으로 \-pub\_date 로 사용할 경우 내림차순으로 정리되며 '-' 사용하지 않으면 오름차순이다. 더불어 ? 사용하면 무작위 정렬이다.

```
Entry.objects.order_by(?)
```

query expressions 로 asc(), desc() 사용하여 정렬할 수도 있다.


* reverse() : reverse Method 는 queryset의 요소의 순서가 바뀌어서 반환한다. 다시 reverse() 를 호출하면 정상방향으로 바뀐다.

```
my_queryset.reverse()[:5]
```
마지막 5번째까지의 항목을 검색하는 방법이다. 그러나 이 방법은 끝에서부터 슬라이싱 하는 것고 같지는 않다. 

위의 예는 마지막 요소를 찾고 그 다음요소를 찾는다고 보면  
[-5:] 는 마지막에서 다섯번째 위치한 요소가 먼저 보인다.

> Django 는 SQL 에서 효율적으로 수행 할 수 없으므로 마지막으로부터 슬라이싱하는 것을 지원하지 않는다.
> 
> 또한 reverse() 는 일반적으로 정의 된 순서가 있는 QuerySet 에서만 호출해야한다.

* distinct(*fields) : SELECT DISTINCT 새로운 QuerySet을 반환한다. 중복항이 제거된 결과를 갖는다.  

> 기본적으로는 QuerySet은 중복 행을 제거하지 않는다. 

* vlues(\*fileds, \*\*expressions) : dict 형 QuerySet을 반환한다. fields 를 지정하면 각 dict에 지정된 keys/values 만 포함된다. fields를 지정하지 않는다면 데이터베이스 테이블의 모든 fields에 대한 key/values가 포함된다.

```
>>> Blog.objects.values()
<QuerySet [{'id':1,'name':'Beatles BLog','tagline':'All the latest Beatles news.'}]>
>>> Blog.objects.values('id','name')
<QuerySet [{'id':1, 'name':'Beatles Blog'}]>
```

values() 를 연속으로 사용해야 하는 경우
 
```
>>>Blog.objects.values('author', entries=Count('entry'))
<QuerySet [{'author':1, 'entries':20}, {'author':1, 'entries':13}]>
>>> Blog.objects.values('author').annotate(entries=Count('entry'))
<QuerySet [{'author':1, 'entries':33}]>
```


* values_list(*fields, flat=False) : values 와 비슷하지만 차이점은 튜플을 반환한다. 각 튜플은 첫번째 item 은 첫번째 field가 된다. 

```
>>>Entry.objects.values_list('id','headline')
[(1,'First entry'), ...]
>>>from django.db.models.functions import Lower
>>>Entry.objects.values_list('id', Lower('headline'))
[(1, 'first entry'), ...
```

```
>>> Entry.objects.values_list('id').order_by('id')
[(1,),(2,),(3,), ...]
>>> Entry.objects.values_list('id', flat=True).order_by('id')
[1,2,3,....]
```

> flat = True 면 반환 결과가 단일 튜플이 아닌 단일 값이다. 

* dates(field, kind, order='ASC') : 모든 가능한 날짜를 나타내는 QuerySet 이다.  
fields 는 DateField 의 이름이여야 한다. datetime.date 객체는 주어진 유형으로 truncated(잘린) 된다. 

```
>>>Entry.objects.dates('pub_date', 'year')
[datetime.date(2005,1,1)]
>>>Entry.objects.dates('pub_date', 'month')
[datetime.date(2005.2,1), datetime.date(2005,3,1)]
>>>Entry.objects.dates('pub_date', 'day')
[datetime.date(2005,2,20),datetime.date(2005,3,20)]
>>>Entry.objects.date('pub_date','day',order='DESC')
[datetime.date(2005,3,20),datetime.date(2005,3,20)]
>>>Entry.objects.filter(headline__contains='Lennon').dates('pub_date', 'day')
[datetime.date(2005,3,20)]
```

* datetimes(field_name, kind,order='asc',tzinfo=None) : datetimeField의 이름인 필드네임을 가지고 있어야 한다. dates 와 다른점은 시간까지 적용한다는 것이다. 시간과 관계하여 settings 의 tz을 설정하면 된다.(ko-kr)

* none() : none() 을 호출하면 객체를 반환하지 않는 QuerySet을 생성한다. qs.none() queryset은 빈쿼리셋의 인스턴스이다.

```
>>>Entry.objects.none()
<QuerySet []>
>>>isinstance(Entry.objects.none(), EmptyQuerySet)
True
```

* all() : 현재의 사본을 반환한다. 결과에 대한 추가적인 필터링을 할때 유용하게 사용될 수 있다.  
QuerySet은 평가되면 결과를 캐시된다.  

* union(*other_qs, all=False) : 두개 혹은 다수의 QuerySet을 결합한다. 

```
>>> qs1.union(qs2,qs3) # qs1에 qs2, qs3 을 결합한다.
```

* intersection(*other_qs) : 두개 혹은 다수의 QuerySet에 공통적으로 들어있는 요소를 반환한다. 

```
>>>qs1.intersection(qs2, qs3) # qs1 에 포함된 요소 중 qs2, qs3 에 공통으로 포함되어 있는 요소를 반환한다. 
```

* difference(*other_qs) : SQL의 EXCEPT를 사용하여 QuerySet의 다른 요소만 남긴다. 

* select_related(*fields) : Foreign_key 관계를 따르는 QuerySet을 반환한다. 

* extra(select=None, where=None, params=None, tables=None, order_by=None, select_params=None) : Django query 로 복잡한 where 절을 표현하기 힘들다. 이럴 때 extra() QuerySet을 사용하여 얻어진 SQL에 hook 을 주입(?)


* select : select 인자는 추가의 필드를 넣을 수 있다. SQL 절로 맵핑하는 dict 이여야 한다. 

```
Entry.objects.extra(select={'is_recent':"pub_date > '2006-01-01'"})
```

각 Entry 객체는 is\_recent 추가속성 이 pub\_date가 2006.jan.1. 보다 큰 값인가를 불린으로 나타낸다. 

```
Blog.objects.extra(
	select={
		'entry_count':'SELECT COUNT(*) FROM blog_entry WHERE blog_entry.blog_id = blog_blog.id'
		},
	)
```

* where/table : where 를 사용하여 명시적이지 않은 SQL을 정의할 수 있다.   
where 와 table 은 문자열 목록을 만들고 모든 where 매개변수는 다른 조건들과 "and" 이다. 

```
Entry.objects.extra(where=["foo='a' or bar = 'a'", "baz = 'a'"])
```

* order_by : 
