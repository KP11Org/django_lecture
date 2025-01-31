**Лекция: Формы Django и ModelForms**  
**Продолжительность**: 60-75 минут  

---

### **1. Введение в Django Forms**  
**Что такое формы?**  
- Инструменты для сбора и проверки пользовательского ввода (например, входа в систему, регистрации, поиска).  
- Генерировать HTML-формы, проверять данные и отправлять сообщения об ошибках.  

**Зачем использовать Django Forms?**  
- **Безопасность**: Автоматически обрабатывать защиту CSRF.  
- **Проверка**: Убедитесь, что данные соответствуют критериям (например, формат электронной почты, обязательные поля).  
- **Эффективность**: Упрощение рендеринга и обработки форм.  

---

### **2. Создаем базовую форму**  
#### **Пример: Контактная форма**  
```python  
# forms.py  
from django import forms  

class ContactForm(forms.Form):  
    name = forms.CharField(max_length=100)  
    email = forms.EmailField()  
    message = forms.CharField(widget=forms.Textarea)  
```  

#### **Отображение формы в шаблоне**  
```html  
<form method="POST">  
    {% csrf_token %}  
    {{ form.as_p }}  
    <button type="submit">Send</button>  
</form>  
```  

#### **Обработка формы в представлении**  
```python  
# views.py  
from django.shortcuts import render, redirect  

def contact_view(request):  
    if request.method == 'POST':  
        form = ContactForm(request.POST)  
        if form.is_valid():  
            # Process data (e.g., send email)  
            return redirect('success')  
    else:  
        form = ContactForm()  
    return render(request, 'contact.html', {'form': form})  
```  

---

### **3. ModelForms: Формы, привязанные к моделям**  
**Что такое ModelForms?**  
- Автоматическое создание форм из моделей Django.  
- Упрощение операций CRUD (создание, чтение, обновление, удаление).  

#### **Пример: Запись в блоге ModelForm**  
```python  
# models.py  
from django.db import models  

class Post(models.Model):  
    title = models.CharField(max_length=200)  
    content = models.TextField()  
    published_date = models.DateTimeField(auto_now_add=True)  

# forms.py  
from django.forms import ModelForm  
from .models import Post  

class PostForm(ModelForm):  
    class Meta:  
        model = Post  
        fields = ['title', 'content']  # Fields to include in the form  
```   

#### **Сохранение данных с помощью ModelForms**  
```python  
# views.py  
def create_post(request):  
    if request.method == 'POST':  
        form = PostForm(request.POST)  
        if form.is_valid():  
            form.save()  # Saves directly to the database  
            return redirect('post_list')  
    else:  
        form = PostForm()  
    return render(request, 'create_post.html', {'form': form})  
```   

---

### **4. Настройка форм**  
1. **Пользовательская проверка**:  
   ```python  
   class ContactForm(forms.Form):  
       # ... fields ...  

       def clean_email(self):  
           email = self.cleaned_data.get('email')  
           if not "example.com" in email:  
               raise forms.ValidationError("Please use a valid email.")  
           return email  
   ```    
2. **Пользовательские виджеты**:  
   ```python  
   class PostForm(ModelForm):  
       class Meta:  
           model = Post  
           fields = ['title', 'content']  
           widgets = {  
               'content': forms.Textarea(attrs={'rows': 4, 'class': 'my-textarea'}),  
           }  
   ```     
3. **Исключение полей**:  
   ```python  
   class PostForm(ModelForm):  
       class Meta:  
           model = Post  
           exclude = ['published_date']  
   ```  

---

### **5. Рекомендации**  
1. **Используйте `is_valid()`**: Всегда проверяйте данные формы перед обработкой.  
2. **Избегайте необработанных HTML-форм**: Используйте функции защиты форм в Django.  
3. **Сохраняйте логику в формах**: Минимизируйте проверку в представлениях.  
4. **Повторно используйте формы**: Создавайте базовые формы для общих шаблонов.  

---

### **Домашнее задание**  
**Цель**: Создать **Систему рецензирования книг**, используя Django Forms и ModelForms.  

#### **Задачи**  
1. **Настройка**:  
   - Создание проекта Django `book_reviews` и приложения `reviews`.  

2. **Модель**:  
   - Определите модель "Книга" с помощью "название", "автор", "краткое содержание" и "дата публикации".  

3. **ModelForm**:  
   - Создайте "Форму книги", привязанную к модели "Книга".  
   - Исключите `published_date` (автоматически заполните его при сохранении).  

4. **Обработка формы**:  
   - Создайте представление для добавления новых книг, используя "BookForm".  
   - Добавьте пользовательскую проверку: убедитесь, что длина заголовка не менее 5 символов.  

5. **Шаблон**:  
   - Создайте шаблон для отображения формы с сообщениями об ошибках.  
   - Оформите форму с помощью CSS (например, классов Bootstrap).  

6. **Бонус**:  
   - Добавить `Форму рецензирования" (не являющуюся формой модели) с полями "имя_рецензента" и `комментарий`.  
   - Связать рецензии с книгами, используя `внешний ключ`.  

#### **Отправка**:  
- Ссылка на репозиторий на GitHub.  
- Скриншоты из:  
  - Форма для отправки книги.  
  - Ошибка проверки в действии.    
