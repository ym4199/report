##Form.errors
dict 형태로 되어 있으며 이를 분해해보면 각 키에 대해서 error 들이 list 형태로 되어있다.  
하나의 키 에 대해서 여러가지 에러가 발생할 수 있기 때문이다.  

여러 에러들을 하나로 합쳐주기 위해 .join으로 합쳐준다

```
result = '<br>'.join(['<br>'.join(v) for v in form.errors.values()])
```

##modify
form 을 이용해 객체를 update 시킴(data에 포함된 부분 중 원본과 다른 부분만 update 된다)

수정 하고 난 뒤 디테일로 가는 문제를 수정 한 뒤에 다시 리스트의 현재 사진 위치로 오도록 만들고 싶다

?next={{ request.path }}#post-{{ comment.post.pk }}

이처럼 해당 url 을 가져오기 위해 표식을(?) 남긴다.

form의 action에 아무값도 없다면 현재페이지로 돌아가게 된다.

```
if request.method == 'POST':
    # Form을 이용해 객체를 update시킴 (data에 포함된 부분만 update됨)
    form = CommentForm(data=request.POST, instance=comment)
    if form.is_valid():
        form.save()
        if next:
            return redirect(next)
        return redirect('post:post_detail', post_pk=comment.post.pk)
```


## decorator
require_POST 는 GET 요청이 왔을 때 아무 동작도 하지 않는다.  
decorator 를 새로 정의하기 위해 decorator 만 따로 분리해서 관리한다.  


## hash tag

```
	# 해시태그에 해당하는 정규표현식 re 는 정규표현식 표준모듈이고 나중에 패턴 확인을 빠르게 하기 위해 패턴을 컴파일한다.
p = re.compile(r'(#\w+)')
	# findall메서드로 해시태그 문자열들을 가져옴
tag_name_list = re.findall(p, self.content)
	# 기존 content(Comment내용)을 변수에 할당
ori_content = self.content
	# 문자열들을 순회하며
for tag_name in tag_name_list:
    # Tag객체를 가져오거나 생성, 생성여부는 쓰지않는 변수이므로 _처리
    tag, _ = Tag.objects.get_or_create(name=tag_name.replace('#', ''))
    # 기존 content의 내용을 변경
    change_tag = '<a href="{url}" class="hash-tag">{tag_name}</a>'.format(
        # url=reverse('post:hashtag_post_list', args=[tag_name.replace('#', '')]),
        url=reverse('post:hashtag_post_list', kwargs={'tag_name': tag_name.replace('#', '')}),
        tag_name=tag_name
    )
```

> get\_or\_create(defualt=None, **kwargs)  
> tuple값이 반환되며 첫번째는 검색되거나 생성된 객체,  
> 두번째는 객체가 작성되었는지에 대한 boolean값이 반환  
> 이미 존재하는 경우 False 가 반환되고 새로 생성되면 True 값이 나온다.


```
<p class="comment-content">{{ comment.html_content|safe }}</p>
```
> escpae : html 요소를 변환시킨다는 것.
> safe : escape 가 필요하지 않은 것으로 본다.

## paginator
> paginator 형 객체를 가져온다. 

```
# Paginator객체 생성, 한 페이지당 3개씩
p = Paginator(all_posts, 3)

# GET parameter에서 'page'값을 page_num변수에 할당
page_num = request.GET.get('page')

# Paginator객체에서 page메서드로 page_num변수를 인수로 전달하여 호출
```

> has_next()  
> has_previous()  
> page(0) 은 없으므로 error 발생시킨다
> page = request.GET.get('page')  
> num_pages : 총 몇페이지 따라서 마지막 페이지(최신) 수를 가리킨다.  
> 페이지가 없으면 None 이 할당 


page 클래스 형 객체를 리턴 : page(*args, **kwargs)  
p 는 paginator 형 객체이고 타입 paginator 형 클래스 
따라서 p 는 paginator 인스턴스 / self 로 시작하는 인스턴스메서드 / 결국 마지막에 page 형 객체를 리턴


## custom_tags
custom tag 를 사용하기 위해 

custom_tags.py 의 메서드에 decorator 사용해야하고
상단에 register=template.Library() 를 기입하고

사용할 곳에서 상단에 {% load custom_tags %} 를 사용할 수 있도록 기입한다.

