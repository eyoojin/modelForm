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
from .models import Article

# Register your models here.
admin.stie.register(Article)
```

---commit---

- createsuperuser
```shell
python manage.py createsuperuser
```

- ../templates/'base.html'
```python
TEMPLATES = [{ 'DIRS': [BASE_DIR / 'templates']}]
```



