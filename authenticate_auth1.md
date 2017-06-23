# Authenticate(Auth1)

Authenticate()   
: 인증(사용자인지)

정상적으로 통과하면 사용유저를 확인

```
user = authenticate(username='이름', password='비밀번호'
# 이 정보를 가지고 
if user is not None:
# user가 있는지를 확인하고
# 있다면 이곳을 실행하고
else:
# 없다면 다음을 실행한다.
```

session 서버쪽에 어떤 value 값을 저장하고 있는 것

middleware : reponse 와 request 사이에서 관리


is_authenticated() : 현재 유저가 로그인 되어있는지 안되어있는지 확인 할 수 있다. 

login(request, user) : request, user 필수