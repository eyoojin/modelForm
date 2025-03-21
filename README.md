# 0. setting

- 가상환경 생성 / 활성화

- django 설치

- .gitignore 설정

---commit---

# 1. Django

- startproject

- startapp / 등록

---commit---

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

---commit---

- admin 등록
```python
# admin.py
from .models import Article

admin.stie.register(Article)
```

---commit---

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
<body>
    <h1>base</h1>
    {% block body %}
    {% end block %}
</body>
```

---commit---

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

---commit---

- articles/'forms.py'
```python
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

---commit---