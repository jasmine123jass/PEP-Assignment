# PEP-Assignment
Assignment 1 -  Build Your First Student API

## 1. Add DRF in settings.py
```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'app',
]

```
---

## 2. Create Student Model
app/models.py
```
from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    course = models.CharField(max_length=100)

    def __str__(self):
        return self.name
```
---
## 3.Serializer
app/serializers.py
```
from rest_framework import serializers
from .models import Student

class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = ['id', 'name', 'age', 'course']
```
## 4.API View
app/views.py
```
from rest_framework.decorators import api_view
from rest_framework.response import Response
from .models import Student
from .serializers import StudentSerializer

@api_view(['GET'])
def student_list(request):
    students = Student.objects.all()
    serializer = StudentSerializer(students, many=True)
    return Response(serializer.data)
```
## 5.URL Configuration
app/urls.py
```
from django.urls import path
from .views import student_list

urlpatterns = [
    path('students/', student_list),
]
```
project/urls.py
```
from django.urls import path, include

urlpatterns = [
    path('api/', include('app.urls')),
]
```







