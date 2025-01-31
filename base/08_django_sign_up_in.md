**Лекция: Регистрация на Django и авторизация с улучшенной безопасностью и настройкой**  

---

### **Цель**  
К концу этой лекции студенты смогут:  
1. Создать безопасную систему аутентификации пользователей (регистрация, логин и сброс пароля) с помощью Django.  
2. Настройте встроенные формы и шаблоны аутентификации в Django.  
3. Ограничьте доступ к представлениям в зависимости от статуса аутентификации.  
4. Внедрите основные методы обеспечения безопасности и обратную связь с пользователями.  

---

### **1. Введение в аутентификацию Django**  
Django.contrib.auth от Django предоставляет надежную, готовую к использованию систему аутентификации. Ключевые функции:  
- **Модели пользователей**: Храните учетные данные пользователя и данные профиля.  
- **Представления аутентификации**: Готовые представления для входа в систему, выхода из системы и управления паролями.  
- **Безопасность**: Хэширование паролей, защита CSRF и управление сеансами.  

**Ключевые понятия**:  
- **Регистрация**: Создание новых учетных записей пользователей.  
- **Вход в систему**: Аутентификация пользователей с использованием учетных данных.  
- **Сеансы**: Поддержание состояния пользователя с помощью файлов cookie.  

---

### **2. Настройка проекта**  
#### Шаг 1: Создайте проект и приложение  
```bash  
django-admin startproject auth_project  
cd auth_project  
python manage.py startapp users  
```   

#### Шаг 2: Обновите настройки  
Добавьте "users" в "INSTALLED_APPS" в "settings.py`:  
```python  
INSTALLED_APPS = [  
    ...  
    'users',  
]  
```    

#### Шаг 3: Перенос базы данных  
```bash  
python manage.py migrate  
```   

---

### **3. Внедрение системы регистрации**  
#### Шаг 1: Пользовательская форма создания пользователя  
Создайте "users/forms.py`, чтобы добавить подтверждение электронной почты:  
```python  
from django import forms  
from django.contrib.auth.forms import UserCreationForm  
from django.contrib.auth.models import User  

class CustomUserCreationForm(UserCreationForm):  
    email = forms.EmailField(required=True, help_text="Enter a valid email address.")  

    class Meta:  
        model = User  
        fields = ["username", "email", "password1", "password2"]  

    def clean_email(self):  
        email = self.cleaned_data.get('email')  
        if User.objects.filter(email=email).exists():  
            raise forms.ValidationError("This email is already registered.")  
        return email  
```   

#### Шаг 2: Просмотр регистрации  
В `users/views.py`:  
```python  
from django.shortcuts import render, redirect  
from django.contrib.auth import login  
from django.contrib import messages  
from .forms import CustomUserCreationForm  

def signup(request):  
    if request.method == 'POST':  
        form = CustomUserCreationForm(request.POST)  
        if form.is_valid():  
            user = form.save()  
            login(request, user)  
            messages.success(request, "Account created successfully!")  
            return redirect('profile')  
        else:  
            messages.error(request, "Registration failed. Fix errors below.")  
    else:  
        form = CustomUserCreationForm()  
    return render(request, 'users/signup.html', {'form': form})  
``` 

#### Шаг 3: Шаблон регистрации  
Создайте `users/templates/users/signup.html`:  
```html  
{% load static %}  
<!DOCTYPE html>  
<html>  
<head>  
    <title>Sign Up</title>  
    <link href="{% static 'css/style.css' %}" rel="stylesheet">  
</head>  
<body>  
    <h2>Sign Up</h2>  
    {% if messages %}  
        {% for message in messages %}  
            <div class="alert alert-{{ message.tags }}">{{ message }}</div>  
        {% endfor %}  
    {% endif %}  
    <form method="post">  
        {% csrf_token %}  
        {{ form.as_p }}  
        <button type="submit">Sign Up</button>  
    </form>  
    <p>Already have an account? <a href="{% url 'login' %}">Log In</a></p>  
</body>  
</html>  
```  

#### Шаг 4: Настройка URL-адреса  
В `users/urls.py`:  
```python  
from django.urls import path  
from .views import signup  

urlpatterns = [  
    path('signup/', signup, name='signup'),  
]  
```   

---

### **4. Реализация входа в систему**  
#### Шаг 1: Используйте Django LoginView  
В `users/urls.py`:  
```python  
from django.contrib.auth.views import LoginView  

urlpatterns += [  
    path('login/', LoginView.as_view(template_name='users/login.html'), name='login'),  
]  
```   

#### Шаг 2: Пользовательский шаблон входа в систему  
Создайте `users/templates/users/login.html`:  
```html  
<h2>Log In</h2>  
{% if form.errors %}  
    <div class="alert alert-danger">Invalid username or password.</div>  
{% endif %}  
<form method="post">  
    {% csrf_token %}  
    {{ form.as_p }}  
    <button type="submit">Log In</button>  
</form>  
```  

---

### **5. Лучшие практики обеспечения безопасности**  
- **HTTPS**: Всегда используйте HTTPS в рабочей среде для шифрования данных.  
- **Ограничение скорости**: Используйте библиотеки, подобные `django-ratelimit`, для предотвращения атак методом перебора.  
- **Политика паролей**: Применяйте надежные пароли с помощью валидаторов Django.  

---

### **6. Домашнее задание**  

#### **Часть 1: Страница профиля с защитой**  
1. Создайте раздел "профиль", доступный только для зарегистрированных пользователей ("@login_required").  
2. Укажите адрес электронной почты пользователя, его последний логин и ссылку для смены пароля.  
3. Добавьте кнопку выхода из системы, которая использует Django "LogoutView".  

---

### **7. Рекомендации по подаче заявок**  
- Отправьте ссылку на репозиторий GitHub с вашим кодом и `README.md".  
