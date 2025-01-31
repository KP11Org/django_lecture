**Лекция: Статические и мультимедийные файлы Django**

---

### **Цель**    
1. Настраивать **статические файлы** (CSS, JavaScript, изображения) в Django и управлять ими.  
2. Обрабатывайте **загруженные пользователем медиафайлы** (например, фотографии профиля, документы).  
3. Используйте статические/медиафайлы в средах разработки и производства.  
4. Используйте теги шаблонов Django и рекомендации по безопасности.  

---

### **1. Введение в статические и мультимедийные файлы**  
- **Статические файлы**: Готовые файлы для оформления и функциональности (например, "styles.css", "logo.png", "scripts.js`).  
- **Медиафайлы**: Пользовательский контент, загружаемый во время выполнения (например, изображения профиля, PDF-файлы).  
- **Ключевое отличие**:  
  - Статические файлы являются частью вашей кодовой базы.  
  - Медиафайлы являются динамическими и хранятся отдельно.  

---

### **2. Настройка статических файлов**  
#### Шаг 1: Настройка проекта  
Создаем проект и приложение на Django:  
```bash  
django-admin startproject static_media_demo  
cd static_media_demo  
python manage.py startapp core  
```  

#### Шаг 2: Обновите `settings.py`  
Добавьте настройки для статических файлов:  
```python  
# settings.py  
STATIC_URL = '/static/'  
STATICFILES_DIRS = [BASE_DIR / 'static']  # Каталог для статических файлов, относящихся к конкретному приложению  
STATIC_ROOT = BASE_DIR / 'staticfiles' # Собранные статические файлы для производства  

MEDIA_URL = '/media/'  
MEDIA_ROOT = BASE_DIR / 'media' # Каталог для загруженных пользователем файлов  
```  

#### Шаг 3: Создайте статические файлы  
1. Создайте "статическую" папку в корневом каталоге вашего проекта.  
2. Добавьте подкаталоги:  
   ```  
   static/  
   ├─ css/  
   │  └─ styles.css  
   ├─ js/  
   │  └─ scripts.js  
   └─ images/  
      └─ logo.png  
   ```    

#### Шаг 4: Используйте статические файлы в шаблонах  
```html  
{% load static %}  
<!DOCTYPE html>  
<html>  
<head>  
    <link rel="stylesheet" href="{% static 'css/styles.css' %}">  
    <script src="{% static 'js/scripts.js' %}"></script>  
    <img src="{% static 'images/logo.png' %}" alt="Logo">  
</head>  
</html>  
```  

#### Шаг 5: Запустите `collectstatic` (Для производства)  
```bash
python manage.py collectstatic # Копирует статические файлы в STATIC_ROOT  
```  

---

### **3. Настройка медиафайлов**  
#### Шаг 1: Создайте модель с загрузкой файла  
В `models.py`:  
```python  
from django.db import models  

class UserProfile(models.Model):  
    name = models.CharField(max_length=100)  
    avatar = models.ImageField(upload_to='avatars/')  # Saves to media/avatars/  
    resume = models.FileField(upload_to='resumes/')   # Saves to media/resumes/  
```   

#### Шаг 2: Создайте форму для загрузки файла  
В `forms.py`:  
```python  
from django import forms  
from .models import UserProfile  

class ProfileForm(forms.ModelForm):  
    class Meta:  
        model = UserProfile  
        fields = ['name', 'avatar', 'resume']  
```  

#### Шаг 3: Обработайте загрузку файла в представлении  
В `views.py`:  
```python  
from django.shortcuts import render, redirect  
from .forms import ProfileForm  

def upload_profile(request):  
    if request.method == 'POST':  
        form = ProfileForm(request.POST, request.FILES)  # Include request.FILES!  
        if form.is_valid():  
            form.save()  
            return redirect('success')  
    else:  
        form = ProfileForm()  
    return render(request, 'upload.html', {'form': form})  
```   

#### Шаг 4: Шаблон для загрузки файлов  
Создайте `templates/upload.html`:  
```html  
<form method="post" enctype="multipart/form-data">  <!-- Required for file upload -->  
    {% csrf_token %}  
    {{ form.as_p }}  
    <button type="submit">Upload</button>  
</form>  
```  

#### Шаг 5: Отправка медиафайлов в процессе разработки  
В `urls.py`:  
```python  
from django.conf import settings  
from django.conf.urls.static import static  

urlpatterns = [  
    # ... your paths  
]  

if settings.DEBUG:  
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)  
``` 

---

### **4. Безопасность и лучшие практики**  
1. **Проверка**: Ограничьте типы/размеры файлов (например, используйте "валидаторы" в "FileField").  
2. **Производство**: Никогда не предоставляйте файлы через Django в производстве. Воспользуйся:  
   - **Статические файлы**: CDN (например, AWS S3) или промежуточное программное обеспечение whitenoise.  
   - **Медиафайлы**: Настройте свой веб-сервер (Nginx/Apache) или облачное хранилище.  
3. **Безопасность**: Очистите имена файлов, чтобы предотвратить атаки с обходом пути.  

---

### **5. Домашнее задание**  

#### **Часть 1: Настройка статических файлов**  
1. Создайте приложение Django с именем `блог`.  
2. Добавьте файл "static/blog/css/styles.css" для оформления записей в блоге.  
3. Используйте "{% static %}" в шаблонах для загрузки CSS и изображения заголовка.  

#### **Часть 2. Обработка медиафайлов**  
1. Создайте модель "Post" с полем "ImageField" для изображений в блоге.  
2. Создайте форму и просмотр для загрузки сообщений с изображениями.  
3. Отобразите загруженные изображения на странице блога.  

#### **Часть 3 (расширенная): Настройка производства**  
1. Установите whitenoise и настройте его для работы со статическими файлами.  
---

### **6. Рекомендации по отправке**  
- Предоставьте репозиторий на GitHub с:   
  - `README.md`, Скриншоты рабочих статических/мультимедийных файлов.  
---

### **7. Ресурсы**  
- [Документы по статическим файлам Django](https://docs.djangoproject.com/en/4.2/howto/static-files/)  
- [Загрузка файлов Django](https://docs.djangoproject.com/en/4.2/topics/http/file-uploads/)  
- [Документация по Whitenoise](http://whitenoise.evans.io/)  
