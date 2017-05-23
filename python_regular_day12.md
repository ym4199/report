# python_day12

## Regular Expressions

### 특정한 패턴에 일치하는 복잡한 문자열을 처리할 때 사용하는 기법.

>파이썬은 표준모듈 re 사용해서 정규표현식을 사용할 수 있다.


```
import re
result = re.match('lux','lux, the lady of luminosity'
```

match의 첫번째 인자에는 패턴이 들어간다.  
두번째 인자에는 문자열 소스가 들어간다.

이때, 소스와 패턴이 일치할 경우 반환한다.


## match
>시작부터 일치하는 패턴

```
import re
source = 'lux, the lady of luminosity'
m=re.match('lux', source)
if m:
	print(m.group())

lux
```

> 이때 문장 전체의 시작부터 일치 여부를 판단하기 때문에 match 안의 첫번째 인자를 lux 외의 다른 문자로 입력할 경우 찾을 수 없다.


```
m=re.match('.*lady', source)
if m:
	print(m.group())
	
lux, the lady	
```

> . 은 문자 1개를 나타내고  
> * 해당 패턴이 0회 이상 온다는 의미  
> .*lady 는 lady 앞에 어떤 패턴이 올 수 있다는 것을 의미하며 lady 로 끝나는 것을 나타낸다.


## search 
> 첫번째 일치하는 패턴 찾기

```
m=re.search('of', source)
if m:
	print(m.group())

of	
```

> * 패턴 없이 of 만 찾을 경우, 문자열 전체에 대해 일치하는 부분을 찾는다


## findall
>일치하는 모든 패턴 찾기

```
m=re.findall('u',source)
print(m)

['y','y']
```

## split
>패턴으로 나누기

```
m=re.split('o',source)
print(m)


['lux, the lady ', 'f lumin', 'sity']
```

## sub
>패턴 대체하기

```
m=re.sub('o','!',source)
print(m)
```

첫인자로 바꿀 대상을 선택하고 두번째 인자로 무엇으로 대체할 것인지 넣는다.


## 패턴 문자

패턴|문자
:---:|:---:
\\d|숫자
\\D|비숫자
\\w|문자
\\W|비문자
\\s|공백문자
\\S|비공백문자
\\b|단어경계
\\B|비단어경계


## 패턴 지정자
>expr 은 정규표현식을 말한다

패턴| 의미
:---:|:---:
abs|리터럴abc
(expr)|expr
expr1\|expr2|expr1 또는 expr2
\.|\\n을 제외한 모든 문자
^|소스문자열의 시작
$|소스문자열의 끝
expr?|0또는 1회의 expr
expr*|0회 이상의 최대 expr
expr*?|0회 이상의 최대 expr
expr+|1회 이상의 최대 expr
expr+?|1회 이상의 최소 expr
expr{m}|m회의 expr
expr{m,n}|m에서 n회의 최대 expr
expr{m,n}?|m에서 n회의 최소 expr
[abc]|a or b or c
[^abc]|not(a or b or c)
expr1(?=expr2)|뒤에 expr2가 오면 expr1에 해당하는 부분
expr1(!=expr2)|뒤에 expr2가 오지 않으면 expr1에 해당하는 부분
(?<=expr1)expr2|앞에 expr1이 오면 expr2에 해당하는 부분
(?<!expr1)expr2|앞에 expr1이 오지 않으면 expr2에 해당하는 부분