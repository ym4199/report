# 170613\_django\_instagram

### ImageField(upload\_to = None)

upload_to = 기본값을 사용하면 저장장소는 MEDIA\_ROOT로 지정된다. 관리적 측면에서 다른 폴더를 지정하여 관리하는편이 좋다. 따라서 upload\_to 를 사용하여 폴더를 지정한다.  
upload\_to 는 함수처럼 호출된다. 업로드 경로를 위해 None 자리에 위치를 기입한다.


## Models VS Views

models 를 views 를 나누는 기준은 데이터와 기능으로 구분할 수 있다.


## get\_or\_create()

get\_or\_create 메소드의 트릭은 실제로 (객체, 생성자) 튜플을 반환한다는 것입니다.  
첫 번째 요소는 검색하려는 모델의 인스턴스이고  
두 번째 요소는 인스턴스가 생성되었는지 여부를 알려주는 부울 플래그입니다.  
True는 인스턴스가 get_or_create 메소드로 작성되었음을 나타내고 False는 인스턴스가 데이터베이스에서 검색되었음을 의미합니다.

```
def add_Tag(self, tag_name):
    tag, tag_created = Tag.objects.get_or_create(name=tag_name)
    if not self.tags.filter(name=tag_name).exist():
        self.tags.add(tag)
```

위의 예를 보면 get_or_create() 값으로 튜플을 반환하기 때문에 두개의 인자로 받는 것을 확인 할 수 있다.  

* 첫번째 값은 검색한 인스턴스 값 저장 
* 두번쨰 값은 참/거짓 여부를 알려주는 부울형 인자

## get\_template(tmeplate\_name, using=None)

template 을 주어진 이름으로 로드하고 template 객체를 반환해준다. 
성공할때까지 템플릿을 순차적으로 시도한다. 찾지 못하면 **TemplateDoesNotExist** 에러를 발생시킨다.   
**TemplateSyntaxError**	에러는 템플릿은 발견했지만 유효하지 않은 구문을 포함했을때 발생시킨다.  
검색을 특정 템플릿으로 제한하고자 한다면 using 인수에 NAME 을 넣자.

```
template = loader.get_template('post/post_detail.html')
```
template 폴더 내의 post 폴더의 post\_detail.html 템플릿을 가져와 template 으로 선언한다.

## request.FILES

request.FILES 는 request method가 POST이고 \<form\> 이 enctype="multipart/form\-data" 속성일때에 데이터를 포함한다. 이외에는 비어있다.

```
post>views.py
photo=request.FILES['file']



post>

```
