# 170616\_Django\_form

## set_password  
user.password('비밀번호') 로 사용할 경우 '비밀번호'라는 값이 저장된다.   
로그인 인증할때 우리가 넣은 password를 hashing 값과 **일치하지 않기** 때문에 set_password('raw_password) 로 사용해야 한다.  
set_password 는 User객체에 password 값을 직접 저장하지 않는다.

> hashing 은 암호가 잘못 사용되지 않도록 수학적으로 변환하여 임의의 문자열로 바꾸는 과정을 말한다.  
> hash 는 임의의 문자열을 일컫는다.  

## is & ==  
is 는 객체의 동일 여부를 파악하고  
\=\= 는 값의 동일 여부를 파악한다.

## bound form  
만들때 어떤 Data 집어넣어서 from이 Template 생성될때 입력받은 Data 바탕으로 채워진 Input을 생성한다.

```
data = {
	'a':'the A',
	'b':'the B'
	}
form=LoginForm(data)
```

## unbound form  
form 객체에 있는 각 필드들이 입력할 수 있는 빈칸 형태로 만들어지는 빈칸 형태로 만들어지는 빈칸 Input 생성

```
form = LoginForm()
```

## form.is_valid  
걸어준 조건을 유효성 검증  
is_valid를 통과하면 True

### 내장된 유효성검사 외에 추가적으로 하고 싶기 때문에 **Form.clean()** 을 사용한다.

