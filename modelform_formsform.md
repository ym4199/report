# form 이용

require_post --> 반드시 POST 요청만 받는다 : 따라서 request.method == 'POST' 절차가 필요 없어진다

```
post=get_objects_or_404(Post, pk=post_pk)
form = form이름(request.POST)
if form.is_valid(): # 유효성 검사를 하고
	form.save() # 저장을 해준다
	return redirect('post:post_detail',post_pk=post.pk) # 이후 다시 디테일로 보내준다
```


modelform 에서 이미 있는 필드에 대해서는 widgets 메타속성안에서 처리하고  
form 에서는 상위공간에서 정의할 수 있다.


```
class PostFrom(models.ModelForm):
	class Meta:
		model = Post
		fields = [
			'comment',
			]
		widgets = {
			'content':fomrs.TextInput(
				attrs={
					'class':'input-comment',
					'placeholder':'입력',
		}
```

> ModelForm 이기 때문에 Meta 메서드 위에 따로 정의하지 않는다.

```
class PostForm(forms.ModelForm):
	memo = forms.CharField(widget=forms.TextInput(attrs={'size':'10'}))
	class Meta:
		model=Post
		fields=[
			'photo',
			'comment',
		]
```

## subclass

```
view.py 

def post_list(request):
	post=Post.objects.all()
	context={
		'posts':post,
		'post_form':PostForm(),
	}
	return render(request, 'post_list.html', context)
```
> 나타내고 싶은 정보를 context 에 담아서 표현한다.  
> 이떄 keyword 값을 template에서 가져다 쓰고   
> 
