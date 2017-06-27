# login

```
django_login(request, user)

login(request,user,backend=None)
```

django 에서 제공하는 login 을 사용한다. 이때  import 에서 login as django_login 을 사용하여 별칭으로 사용한다. 


```
user =authenticate(request, author = username, password=password,)
```
authenticate 를 통과했을때 자격 증명을 확인한다. 유효한 경우 User를 반환한다.


```
request.user.is_authenticate
```

현재 사용자가 로그인 되어 있다면 User의 인스턴스, 로그인 되어있지 않다면 AnonymousUser 인스턴스가 설정된다.

# logout

```
django_logout(request)

logout(request)
```

django 내에서 제공하는 logout 을 사용한한다.  
역시 logout as django_logout을 선언하여 별칭으로 사용한다.


> login/logout 함수 뷰에서 클래스 뷰로 전환 중 



base.html 상에서 login 상태와 logout 상태에서 보여줄 내용 구분해 줘야한다. 

기본적으로 template 안에 user 라는 변수를 사용할 수 있다.   
왜 ?  requets 안에 있는 변수는 바로 사용가능 하다.   

