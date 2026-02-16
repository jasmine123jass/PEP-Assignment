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
## Assignment-2
Create Student Data Using API (POST)
views.py
```
@api_view(['GET', 'POST'])
def student_list(request):

    if request.method == 'GET':
        students = Student.objects.all()
        serializer = StudentSerializer(students, many=True)
        return Response(serializer.data)

    if request.method == 'POST':
        serializer = StudentSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=201)
        return Response(serializer.errors, status=400)
```
## Assignment 3: ModelSerializer for Student API
Create Student Model
models.py
```
from django.db import models

class Student(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()
    email = models.EmailField()

    def __str__(self):
        return self.name
```
Create StudentSerializer
```
from rest_framework import serializers
from .models import Student

class StudentSerializer(serializers.ModelSerializer):
    class Meta:
        model = Student
        fields = '__all__'
```
views.py
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
urls.py
```
from django.urls import path
from . import views

urlpatterns = [
    path('students/', views.student_list),
]
```
## Assignment 4 - Validation in Serializer
Age must be greater than 5
update serializers.py
```
class StudentSerializer(serializers.ModelSerializer):
    
    class Meta:
        model = Student
        fields = '__all__'

    def validate_age(self, value):
        if value <= 5:
            raise serializers.ValidationError("Age must be greater than 5")
        return value
```







