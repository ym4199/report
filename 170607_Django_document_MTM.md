# Django_documents

## ManyToManyField
### 상위 개념에 해당하는 모델에  manytomanyfield를 부여하자

```
class Lecture(models.Model):
	title= models.CharField(max_length=100)
	students=models.ManyToManyField(
		'Student',
	)
	def __str__(self):
		return self.title
		
class Student(models.Model):
	name = models.CharField(max_length=10)	
```

생각해보자. 물론 학생이 많을 수 있고 수업이 많을 수 있다. 그러나 일반적으로 보다 많은 학생들이 일부의 과목(점수를 잘 주는)을 두고 수강경쟁을 벌인다. 결국 수업을 상위개념으로 두고 학생이 속한다고 하자.  
따라서 Lecture 내에 Student 모델과 연결되는 students를 만들고 mtm으로 연결시키자.  
이제 우리는 Student를 역참조할 수 있게되었다.

```
>>>s1 = Student.objects.create(name='aa')
>>>s1.save()
>>>s2 = Student.objects.create(name='bb')
>>>s2.save()
>>>l1 = Lecture.objects.create(title='wps')
>>>l2 = Lecture.objects.create(title='wpswps')
>>>l1.save()
>>>l2.save()
>>>l1.student.add(s1)
>>>s2.lecture_set.add(l2)
```

위의 코드로 l1의 강의에 s1 학생이 추가 되었고, s2 학생이 l2에 들어가게 되었다. **앞의 인스턴스가 기준**임을 잊지 말자.

이때 Lecture 에 MTM이 되어있기 때문에 lecture_set 으로 student를 역참조 할 수 있게 되었다.

