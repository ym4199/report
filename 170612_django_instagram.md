# 170612\_django\_instagram

## substituting a custom User model

authentication : 사용자 인증(수행가능 작업)  
authorization : 권한 부여

Django 는 사용자 정의 모델을 참조하는 **AUTH\_USER\_MODEL** 설정 값을 제공하여 대체할 수 있다.

```
AUTH_USER_MODEL = 'member.User'
```

User가 정의된 곳이 member 폴더 내의 model 이다. 이때, User 의 매개변수는 AbstractUser이다.

```
class User(AbstracUser):
	pass
```

따라서 Post model의 참조값으로 사용되던 User라는 키 값은 모두 settings.AUTH\_USER\_MODEL 로 변환이 필요하다. 


admin.py 의 내용에 User를 추가해줘야 한다. 앞으로 계속 Django 내장함수 User를 사용한다. 

```
class PostAdmin(admin.ModelAdmin):
    pass

admin.site.register(Post, PostAdmin)
```

**한가지 확실한건 AUTH\_USER\_MODEL 을 해준뒤 makemigrations , migrate를 수행해야 한다는 것이다.** 그렇지 않으면 하나하나 데이터베이스를 수정해야하는 수고스러움을 겪게 된다.

## PHOTO
### ImageField(upload_to=None)

사진, 이미지를 올리기 위한 필드 값이다. 다만 이미지 필드를 사용하기 위해 Pillow 가 설치되어있어야 한다.  
이때, 주의해야 하는 것은 brew 를 통한 설치가 우선시 되어야 한다.

```
>>> brew install libtiff libjpeg webp little-cms2

>>> pip install Pillow
```
위 작업이 완료되어야 이미지 필드를 원활하게 사용가능 하다.

이미지, 사진이 반드시 업로드되어야하는 것은 아니기때문에 blank=True 를 통해 빈 첨부를 고려한다.

더불어 upload_to 가 설정되지 않았다면 앱폴더의 root에 사진이 저장된다. 이는 원하는 바가 아니므로(또한 관리도 어렵다.) 하나의 폴더에 모두 모아본다.

따라서 앱폴더와 동일 선상에 media 폴더를 생성해주고 settings 에서 이곳을 기본 저장 폴더로 인식시켜주자.

/static/ 과 동일한 방법이다.

```
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR,'media')
```
이제부터 media 를 기본 저장 폴더로 사용하고 media 가 붙어 들어오는 url을 해당 폴더로 인식시킨다.

```
urlpatterns += static(
    prefix=settings.MEDIA_URL,
    document_root=settings.MEDIA_ROOT,
)
```
## P.S.

```
def add_Tag(self, tag_name):
    tag, tag_created = Tag.objects.get_or_create(name=tag_name)
    if not self.tags.filter(name=tag_name).exists():
        self.tags.add(tag)
```

get\_or\_create() : 해당 요소를 가져오는데 만약 그 값이 없다면 새로 생성 및 저장을 한다.

get\_user\_model() : 직접 User를 참조하는 대신 get\_user\_model을 사용하여 사용자 모델을 참조해야 한다. 외래키 또는 다대다 관계를 정의할때 AUTH\_USER\_MODEL 설정을 사용하여 사용자 정의 모델을 지정한다.

