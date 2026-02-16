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
## Assignment 5 - Email
Open settings.py
```
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = 'yourgmail@gmail.com'
EMAIL_HOST_PASSWORD = 'your_app_password'
DEFAULT_FROM_EMAIL = EMAIL_HOST_USER
```
views.py 
```
from django.shortcuts import render, redirect
from django.core.mail import send_mail
from .forms import RegistrationForm
from django.conf import settings

def register(request):
    if request.method == "POST":
        form = RegistrationForm(request.POST)
        if form.is_valid():
            user = form.save()

            # Send welcome email
            send_mail(
                subject="Welcome to Our Platform",
                message="Hello, your registration is successful.",
                from_email=settings.DEFAULT_FROM_EMAIL,
                recipient_list=[user.email],
                fail_silently=False,
            )

            return render(request, "success.html")

    else:
        form = RegistrationForm()

    return render(request, "register.html", {"form": form})
```
models.py
```
from django.db import models

class RegisteredUser(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()

    def __str__(self):
        return self.name
```
forms.py
```
from django import forms
from .models import RegisteredUser

class RegistrationForm(forms.ModelForm):
    class Meta:
        model = RegisteredUser
        fields = ['name', 'email']
```

##  Assignment 6 - Email to admin
models.py
```
class Contact(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    message = models.TextField()

    def __str__(self):
        return self.name
```
admin.py
```
from .models import Contact

admin.site.register(Contact)
```
forms.py
```
from .models import Contact

class ContactForm(forms.ModelForm):
    class Meta:
        model = Contact
        fields = ['name', 'email', 'message']
```
views.py
```
from django.contrib import messages
from .forms import ContactForm

def contact_view(request):
    if request.method == "POST":
        form = ContactForm(request.POST)
        if form.is_valid():
            contact = form.save()

            send_mail(
                subject="New Contact Query",
                message=f"""
Name: {contact.name}
Email: {contact.email}
Message: {contact.message}
                """,
                from_email=settings.DEFAULT_FROM_EMAIL,
                recipient_list=[settings.EMAIL_HOST_USER],  # Admin email
                fail_silently=False,
            )

            messages.success(request, "Your message has been sent successfully!")
            return redirect("contact")

    else:
        form = ContactForm()

    return render(request, "contact.html", {"form": form})
```










