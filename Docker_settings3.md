# Docker_settings3

docker 를 이용하여 aws 에 올리고자 할때 .config_secret 폴더 내의 settings\_deploy.json 에 'database' 와 'aws'에 대한 정보를 입력해줘야 한다.

```
"databases":{
  "default":{
    "ENGINE":"django.db.backends.postgresql",
    "NAME":"<데이터베이스 이름>",
    "USER":"<데이터베이스 유저 이름>",
    "PASSWORD":"<해당 유저 비밀번호>",
    "HOST":"RDS 의 호스트 주소",
    "PORT":"5432"
  }
}
```


```
  "aws":{
  "access_key_id":"<인스턴스 키값>",
  "secret_access_key":"<인스턴스 시크릿 키값>",
  "s3_bucket_name":"<s3 의 버킷이름>",
  "s3_signature_version":"s3v4",
  "s3_region_name":"ap-northeast-2"
  }
```

이렇게 지정하여 파일 내부에서 서버에 접근하고 올릴 준비가 다 됐다.

추가로 bucket을 관리하는 s3에 static 파일과 media 파일이 올라가 관리해줘야 하기 때문에 settings > deploy.py 내에 주소를 명시하자.

```
DEFAULT_FILE_STORAGE = 'config.storages.MediaStorage'
STATICFILES_STORAGE = 'config.storages.StaticStorage'
STATICFILES_LOCATION = 'static'
MEDIAFILES_LOCATION = 'media'
STATIC_URL = '/static/'
MEDIA_URL = '/media/'
```

위의 코드를 기입하고 준비가 되었다면 

```
pip install django-storages
```

storages.py 를 config 폴더 내에 만들어 주고 해당 파일에서 저장소에 대해 서술해주자.

```
class StaticStorage(S3Boto3Storage):
    location = settings.STATICFILES_LOCATION
    file_overwrite = True


class MediaStorage(S3Boto3Storage):
    location = settings.MEDIAFILES_LOCATION
    file_overwrite = False

```

잊지말고 base.py 내의 INSTALLED_APPS 내에 storages 를 추가해주자.

docker images 를 생성하기 전에 local 환경에서 정상 작동하는지 확인을 한 후에 build 를 통해 images 를 만들어 주자.

images 가 생성된 후에는 본격적인 서버 운용 준비를 하자. 

[docker 공식사이트](https://cloud.docker.com/swarm/ym4199/dashboard/onboarding/cloud-registry) 에 로그인한 후에 관리할 repository 를 하나 생성한다.

해당 repository 의 이름은 <유저이름>/\<repository이름> 으로 생성이 된다. 

이제 터미널에서 tag 명령을 통해 <이름>/<폴더이름> 형태로 변경해준다.

```
docker tag <기존이름> <이름>/<폴더이름>
```

이후 docker images 를 통해 잘 바뀐 것을 확인하자.

```
docker push <이름>/<폴더이름>
```

git 처럼 내 images 가 docker 가 관리하는 사이트로 업로드된 것을 확인할 수 있다.

이 작업은 오랜 시간이 걸린다. 

### 이후 주의할 것은 .dockerfile 내의 ubuntu 파일이 아닌 dockerfile 로 관리하던 내역의 from 을 <이름>/<폴더이름> 으로 변경 관리해줘야 한다.

> 더불어 dockerfile 을 최상위 폴더 내로 옮기고 Dockerfile로 관리해줘야 한다. 이는 docker 가 dockerfile을 찾게 해주기 위해서다.

## eb settings

이제 eb(elastiBeanstalk) 를 설정하는 일만 남았다. 

> .elasticbeanstalk 를 생성하여 관리하자. .gitignore 로 관리하던 내역 중에 eb 상에 올라가야 하는 내용이 있기 때문이다.

```
eb init
```

명령을 통해 eb의 환경설정을 해주자. 

* 언어
* 프레임 종류

정도를 선택하는 내역이 나온다. asia 와 docker 를 선택하고 나머지 설정은 상황에 맞춰해주자.  
모두 선택했다면 tree 내역에 .elasticBeanstalk 폴더가 생성되었음을 확인할 수 있다. 

> 초기 설정을 잘 못해줬다 싶으면 해당 폴더를 지워주고 다시 eb init 을 진행하자.

```
eb create
```

위의 명령내에 eb deploy 가 내장되어 있다. 오류가 없다면 한번에 생성 및 업로드까지 마칠 수 있다.

만약 중간에 오류가 생성되었다면 해당 오류를 해결한 뒤에 eb deploy 로 모든 코드를 업로드 시켜주자.

> eb deploy 는 server 의 docker 상에 dockerfile 을 비롯한 나의 모든 코드를 압축하여 전송하고 이를 다시 서버단에서 환경에 맞춰 재세팅으로 펼친다. 
> 즉, 이미지가 업로드되는 것이 아니라는 것을 명심하자.