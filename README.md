# 0. setting
### ---commit(.gitignore 설정)---

- 가상환경 생성 / 활성화

- django 설치

- .gitignore 설정

### ---commit(startproject / startapp)---

# 1. Django

- startproject
- startapp / 등록
```shell
django-admin startproject modelForm .
django-admin startapp articles
```
```python
# setting.py
INSTALLED_APPS = ['articles']
```

### ---commit(modeling / migration)---

- modeling
```python
# models.py
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
```

- migration
```shell
python manage.py makemigrations
python manage.py migrate
```

### ---commit(admin에 Article 추가)---

- admin에 Article 추가
```python
# admin.py
from .models import Article

admin.stie.register(Article)
```

### ---commit(공통 base.html 설정)---

- createsuperuser
```shell
python manage.py createsuperuser
```

- ../templates/'base.html'
```python
# setting.py
TEMPLATES = [{ 'DIRS': [BASE_DIR / 'templates']}]
```
```html
<!-- base.html -->
<body>
    <h1>base</h1>
    {% block body %}
    {% end block %}
</body>
```

### ---commit(Read(All) 기능 구현)---

- url
```python
# ../'urls.py'
from django.urls import include

urlpatterns = [path('articles/', include('articles.urls'))]
```
```python
# articles/'urls.py'
from django.urls import path

app_name = 'articles'

urlpatterns = []
```

- Read all
```python
# urls.py
from . import views

path('', views.index, name='index')
```
```python
# view.py
from .models import Article

def index(request):
    articles = Article.objects.all()

    context = {
        'articles': articles,
    }
    
    return render(request, 'index.html', context)
```
```html
<!-- articles/templates/'index.html' -->
{% extends 'base.html' %}

{% block body %}
    {% for article in articles %}
        <h3>{{article.title}}</h3>
        <p>{{article.content}}</p>
    {% endfor %}
{% endblock %}
```

### ---commit(Create(Ver.1) 기능 구현)---

- articles/'forms.py'
```python
# forms.py
from django.forms import ModelForm
from .models import Article

class ArticleForm(ModelForm):
    class Meta():
        model = Article
        fields = '__all__'
```

- Create
```python
# urls.py
path('create/', views.create, name='create')
```
```python
# views.py
from .forms import ArticleForm

def create(request):
    # new/ => 빈 종이를 보여주는 기능
    # create/ => 사용자가 입력한 데이터 저장
    # ============
    # GET create/ => 빈 종이를 보여주는 기능
    # POST create/ => 사용자가 입력한 데이터 저장

    if request.method == 'POST':
        pass
    else:
        form = ArticleForm()
        
        context = {
            'form': form,
        }

        return render(request, 'create.html', context)
```
```html
<!-- create.html -->
{% extends 'base.html'%}

{% block body %}
    {{form}}
{% endblock %} 
```
```html
<!-- action 비워두면 현재 위치로 요청을 보냄 -->
<form action="" method="POST">
    {% csrf_token%}
    {{form}}
    <input type="submit" value="Send">
</form>
```
```python
# views.py
from django.shortcuts import redirect

    if request.method == 'POST':
    form = ArticleForm(request.POST)
    # request.POST: dictionary
    # 내가 만든 폼에 사용자가 입력한 데이터를 넣어줌

        if form.is_valid():
        # 폼에 있는 데이터가 유효한가요?
            form.save()
            return redirect('articles:index')            
        else:
            pass
```
```python
        else:
            context = {
                'form': form,
            }
            return render(request, 'create.html', context)
```

### ---commit(Create(Ver.2) 기능 구현)---

```python
    # else:
    #     pass
    # 쓸모 없는 코드이기 때문에 삭제
else:
    form = ArticleForm()
    
context = {
    'form': form,
}

return render(request, 'create.html', context)
```
```python
# 흐름
def create(request):    
    # 모든 경우의 수
    # - GET: form을 만들어서 html문서를 사용자에게 리턴
    # - POST: invalid data (데이터 검증 실패)
    # - POST: valid data (데이터 검증 성공)

    # 5. POST 요청 (invalid data)
    # 10. POST요청 (valid data)
    if request.method == 'POST':

        # 6. 사용자가 입력한 데이터(request.POST)를 담은 form 생성 (invalid)
        # 11. 사용자가 입력한 데이터(request.POST)를 담은 form 생성 (vaild)
        form = ArticleForm(request.POST)

        # 7. form을 검증 -> 실패
        # 12. form을 검증 -> 성공
        if form.is_valid():

            # 13. form 저장
            form.save()

            # 14. index로 redirect
            return redirect('articles:index')
    
    # 1. GET 요청
    else:

        # 2. 비어있는 form을 만든다
        form = ArticleForm()

    # 3. context dict에 비어있는 form을 담는다
    # 8. context dict에 실패한 form을 담는다
    context = {
        'form': form,
    }

    # 4. create.html을 렌더링
    # 9. create.html을 렌더링
    return render(request, 'create.html', context)
```
