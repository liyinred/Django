![image](https://github.com/liyinred/Django_Wenhao/assets/83255231/146c8936-c69b-41be-a520-43179a70deab)

```bash
pipreqs --ignore .venv --force

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


# myapp/model.py
from django.db import models

class FormSubmission(models.Model):
    products = models.TextField()
    recipient = models.CharField(max_length=100)
    address = models.CharField(max_length=200)
    phone = models.CharField(max_length=15)
    email = models.EmailField()
    institution = models.CharField(max_length=100)
    department = models.CharField(max_length=100)
    pi = models.CharField(max_length=100)
    researchField = models.CharField(max_length=100, null=True, blank=True)
    salesContact = models.CharField(max_length=100, null=True, blank=True)


# myapp/view.py
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
import json
from .models import FormSubmission

@csrf_exempt
def submit_form(request):
    if request.method == 'POST':
        try:
            data = json.loads(request.body.decode('utf-8'))
            required_fields = ['products', 'recipient', 'address', 'phone', 'email', 'institution', 'department', 'pi']
            missing_fields = [field for field in required_fields if not data.get(field)]
            
            if missing_fields:
                return JsonResponse({'success': False, 'error': 'Missing fields', 'missing_fields': missing_fields})

            submission = FormSubmission.objects.create(
                products=','.join(data['products']),
                recipient=data['recipient'],
                address=data['address'],
                phone=data['phone'],
                email=data['email'],
                institution=data['institution'],
                department=data['department'],
                pi=data['pi'],
                researchField=data.get('researchField'),
                salesContact=data.get('salesContact')
            )

            return JsonResponse({'success': True})

        except json.JSONDecodeError:
            return JsonResponse({'success': False, 'error': 'Invalid JSON'})
        except Exception as e:
            return JsonResponse({'success': False, 'error': str(e)})

    return JsonResponse({'success': False, 'error': 'Invalid request method'})
```
