```bash
django-admin startproject myproject

python manage.py startapp myapp

python manage.py makemigrations

python manage.py migrate

python manage.py runserver 127.0.0.1:8000
```

```python
# myproject/setting.py
ALLOWED_HOSTS = ['*']

INSTALLED_APPS = [
    'myapp',
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydatabase',
        'USER': 'root',
        'PASSWORD': '123456',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}

# myproject/urls.py
urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('myapp.urls')),  
]

# myapp/urls.py
urlpatterns = [
    path('submit-form/', submit_form, name='submit_form'),
]
```
