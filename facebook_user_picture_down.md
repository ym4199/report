# facebook\_user\_info

### image down (userprofile picture)

1. 해당 user 의 profile 로 접근 할 수 있는 url
3. 임시저장할 파일객체 : 메모리 상에 위치
4. 위의 위치에서 get 요청
5. 요청 결과를 파일객체에 기록
6. ImgaeField 의 save() 메서드를 호출하여 해당 임시파일객체를 주어진 이름의 파일로 저장

> 동적으로 이름을 만들어 주자. 확장자 등을 우리가 모르기 때문이다.

```
url_picture = user_info['picture']['data']['url']

temp_file = NamedTemporaryFile()

response = reqeusts.get(url_picture)

temp_file.write(response.content)
user.img_profile.save('profile.jpg', File(temp_file)

```

파일 확장자를 가져와서 저장할때 해당 확장자로 저장을 하자.

```
p = re.compile(r'.*\.([^?)+)')
```

파일 이름을 동적으로 만들어 주기 위해

```
file_ext = re.search([, url_picture)
file_name= '{}.{}.format(user.pk, file_ext)
```
이를 통해 해당 user id 로 파일이름을 만들고 확장자를 지정하여 저장한다.


결국 완성된 코드

```
url_picture = user_info['picture']['data']['url']

p = re.compile(r'.*\.([^?]+)')
file_ext = re.search(p, url_picture).group(1)
file_name = '{}.{}'.format(
    user.pk,
    file_ext,
)

temp_file = NamedTemporaryFile()

response = requests.get(url_picture)
temp_file.write(response.content)
user.img_profile.save(file_name, File(temp_file))
```