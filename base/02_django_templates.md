**Лекция: Шаблоны Django**  
**Продолжительность**: 60-75 минут  

---

### **1. Введение в шаблоны Django**  
**Что такое шаблоны?**  
- HTML-файлы с **динамическими заполнителями** для отображения данных из представлений.  
- Разделение *логики представления* (как выглядят данные) от *бизнес-логики* (как обрабатываются данные).  

**Зачем использовать шаблоны?**  
- **Возможность повторного использования**: создание компонентов для повторного использования (например, заголовков, нижних колонтитулов).  
- **Удобство сопровождения**: обновление пользовательского интерфейса без изменения серверного кода.  
- **Гибкость**: использование циклов, условных выражений и фильтров для управления содержимым.  

---

### **2. Язык шаблонов Django (DTL)**  
#### **Основы синтаксиса**  
- **Переменные**: `{{ variable }}`  
  ```html  
  <h1>Welcome, {{ user.username }}!</h1>  
  ```  
- **Теги**: `{% tag %}`  
  - **Циклы**:  
    ```html  
    {% for post in posts %}  
      <p>{{ post.title }}</p>  
    {% endfor %}  
    ```  
  - **Условные конструкции**:  
    ```html  
    {% if user.is_authenticated %}  
      <a href="/logout">Logout</a>  
    {% else %}  
      <a href="/login">Login</a>  
    {% endif %}  
    ```  
- **Фильтры**: преобразуйте переменные с помощью `{{ переменная|фильтр }}`  
  ```html  
  {{ post.content|truncatechars:100 }}  <!-- Сократить текст -->  
  {{ post.date|date:"F j, Y" }}         <!-- Отформатировать дату -->  
  ```  

#### **Наследование шаблонов**  
- **Базовый шаблон** (`base.html`):  
  ```html  
  <!DOCTYPE html>  
  <html>  
  <head>  
      <title>{% block title %}My Blog{% endblock %}</title>  
      <!-- Include CSS/JS here -->  
  </head>  
  <body>  
      <header>{% include "navbar.html" %}</header>  
      <main>{% block content %}{% endblock %}</main>  
      <footer>© 2025 My Blog</footer>  
  </body>  
  </html>  
  ```  
- **Дочерний шаблон** (`home.html`):  
  ```html  
  {% extends "base.html" %}  
  {% block title %}Home Page{% endblock %}  
  {% block content %}  
    <h1>Latest Posts</h1>  
    {% for post in posts %}  
      <div class="post">  
        <h2>{{ post.title }}</h2>  
        <p>{{ post.content|linebreaks }}</p>  
      </div>  
    {% endfor %}  
  {% endblock %}  
  ```   

---

### **3. Организация шаблонов**  
1. **Структура каталогов**:  
   ```  
   myproject/  
     templates/  
       base.html  
       home.html  
       posts/  
         detail.html  
   ```    
2. **Настройка шаблонов в `settings.py`**:  
   ```python  
   TEMPLATES = [  
       {  
           'BACKEND': 'django.template.backends.django.DjangoTemplates',  
           'DIRS': [os.path.join(BASE_DIR, 'templates')],  
           'APP_DIRS': True,  
       },  
   ]  
   ```  

---

### **4. Рекомендации**  
1. **Сведите к минимуму логику в шаблонах**: храните сложную логику в представлениях/моделях.  
2. **Используйте `{% include %}`**: повторно используйте такие компоненты, как верхние и нижние колонтитулы.  
3. **Использование фильтров**: упростите форматирование данных (например, дат, усечение текста).  
4. **Кэширование шаблонов**: используйте `{% cache %}` для страниц с высокой посещаемостью.  

---

### **Домашнее задание**  
**Цель**: создать приложение для блога с использованием шаблонов Django.  

#### **Задачи**  
1. **Настройка**:  
   - Создайте проект Django `myblog` и приложение `posts`.  
   - Установите Django и запустите миграции.  

2. **Модель**:  
   - Определите модель `Post` с полями: `title`, `content`, `author` и `published_date`.  
   ```python  
   from django.db import models  

   class Post(models.Model):  
       title = models.CharField(max_length=200)  
       content = models.TextField()  
       author = models.CharField(max_length=100)  
       published_date = models.DateTimeField(auto_now_add=True)  
   ```  

3. **Представления и URL-адреса**:  
   - Создайте представление для получения всех постов.  
   - Сопоставьте представление с URL-адресом (например, `http://127.0.0.1:8000/posts/`).  
   ```python  
   # posts/views.py  
   from django.shortcuts import render  
   from .models import Post  

   def post_list(request):  
       posts = Post.objects.all()  
       return render(request, 'posts/list.html', {'posts': posts})  
   ```  

4. **Шаблоны**:  
   - Создайте `base.html` с навигационной панелью и нижним колонтитулом.  
   - Создайте шаблон `posts/list.html`, который:  
     - Расширяет `base.html`.  
     - Выводит все посты с помощью цикла.  
     - Форматирует дату с помощью фильтра `date`.  
     - Ограничивает содержимое до 150 символов.  

5. **Интерфейс администратора**:  
   - Зарегистрируйте модель `Post` в `admin.py`.  
   - Добавьте примеры постов через администратора Django.  

#### **Дополнительные задания**  
- Добавьте **detail page** для отдельных постов.  
- Выделите публикации, опубликованные за последние 7 дней, с помощью `{% if %}`.  
- Стилизуйте шаблоны с помощью CSS.  

---

**Отправка**:  
- ссылка на репозиторий GitHub, содержащий ваш код.  
- скриншот главной страницы вашего блога.
