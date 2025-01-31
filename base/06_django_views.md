**Лекция: Django Views**  
**Продолжительность**: 60-75 минут  

---

### **1. Введение в представления Django**  
**Что такое представления?**  
- **Определение**: Функции или классы Python, которые обрабатывают HTTP-запросы и возвращают HTTP-ответы.  
- **Роль в MVT**:  
  - **Модели**: Обработка данных.  
  - **Представления**: Логика процесса.  
  - **Шаблоны**: Вывод на экран.  

**Типы представлений**  
1. **Представления на основе функций (FBV)**: Простое, гибкое, явное управление.  
2. **Представления на основе классов (CBV)**: Многоразовые, простые в использовании, встроенные общие представления.  

---

### **2. Представления на основе функций (FBV)**  
#### **Базовый пример**  
```python  
# views.py  
from django.http import HttpResponse  
from django.shortcuts import render  

def home(request):  
    return HttpResponse("Welcome to the Home Page!")  

def about(request):  
    return render(request, 'about.html')  
```   

#### **Обработка запросов**  
- **GET запросы**: извлекать данные (например, отображать форму).  
- **POST запросы**: отправлять данные (например, обрабатывать форму).  
```python  
def contact(request):  
    if request.method == 'POST':  
        name = request.POST.get('name')  
        # Process data (e.g., send email)  
        return HttpResponse(f"Thanks, {name}!")  
    return render(request, 'contact.html')  
```  

---

### **3. Представления на основе классов (CBV)**  
#### **Общие представления**  
- **TemplateView**: Визуализация шаблона.  
- **ListView**: Отображение списка объектов.  
- **DetailView**: Отображение сведений об отдельном объекте.  

**Пример: ListView**  
```python  
from django.views.generic import ListView  
from .models import Post  

class PostListView(ListView):  
    model = Post  
    template_name = 'posts/list.html'  
    context_object_name = 'posts'  # Default: object_list  
``` 

**Пример: Подробный просмотр**  
```python  
from django.views.generic import DetailView  

class PostDetailView(DetailView):  
    model = Post  
    template_name = 'posts/detail.html'  
``` 

---

### **4. Передача контекстных данных**  
**Пример FBV**  
```python  
def post_list(request):  
    posts = Post.objects.all()  
    return render(request, 'posts/list.html', {'posts': posts})  
``` 

**Пример CBV**  
Переопределите `get_context_data`, чтобы добавить дополнительный контекст:  
```python  
class PostListView(ListView):  
    model = Post  
    template_name = 'posts/list.html'  

    def get_context_data(self, **kwargs):  
        context = super().get_context_data(**kwargs)  
        context['page_title'] = "Latest Posts"  
        return context  
```  

---

### **5. Обработка форм в представлениях**  
**FBV с помощью Form**  
```python  
from django.shortcuts import redirect  
from .forms import PostForm  

def create_post(request):  
    if request.method == 'POST':  
        form = PostForm(request.POST)  
        if form.is_valid():  
            form.save()  
            return redirect('post_list')  
    else:  
        form = PostForm()  
    return render(request, 'posts/create.html', {'form': form})  
```  

**CBV с помощью FormView**  
```python  
from django.views.generic.edit import FormView  
from .forms import PostForm  

class PostCreateView(FormView):  
    template_name = 'posts/create.html'  
    form_class = PostForm  
    success_url = '/posts/'  

    def form_valid(self, form):  
        form.save()  
        return super().form_valid(form)  
```   

---

### **6. Декораторы для видов**  
- **`@login_required`**: Ограничить доступ для пользователей, прошедших проверку подлинности.  
- **`@csrf_exempt`**: Отключить защиту CSRF (использовать с осторожностью).  

**Пример**  
```python  
from django.contrib.auth.decorators import login_required  

@login_required  
def dashboard(request):  
    return render(request, 'dashboard.html')  
```  

**Для CBVS**: Используйте `@method_decorator`  
```python  
from django.utils.decorators import method_decorator  
from django.contrib.auth.decorators import login_required  

@method_decorator(login_required, name='dispatch')  
class DashboardView(TemplateView):  
    template_name = 'dashboard.html'  
```    

---

### **7. Лучшие практики**  
1. **Ограничьте количество просмотров**: Делегируйте бизнес-логику моделям или сервисам.  
2. **Используйте именованные URL-адреса**: избегайте жестко заданных путей (например, `redirect('post_list')`).  
3. **Корректно обрабатывать ошибки**: Возвращать пользовательские 404/500 страниц.  
4. **Тестовые просмотры**: Использовать тестовый клиент Django для имитации запросов.  

---

### **Домашнее задание**  
**Цель**: Создать **приложение для ведения блога**, используя Django views.  

#### **Задачи**  
1. **Настройка**:  
   - Создать проект Django `myblog` и приложение `posts`.  
   - Определите модель `Post` с "title", "content", "author" и "created_at".  

2. **Представления на основе функций**:  
   - Создайте представление `post_list` для отображения всех записей.  
   - Создайте представление "post_detail", чтобы отображать одну запись по идентификатору.  

3. **Представления на основе классов**:  
   - Преобразуйте `post_list` в `ListView`.  
   - Преобразуйте "post_detail" в `DetailView`.  

4. **Обработка форм**:  
   - Создайте "PostForm" в "forms.py`.  
   - Добавьте представление "create_post" для обработки отправки формы (FBV или CBV).  

5. **Аутентификация**:  
   - Ограничить доступ к "create_post" только для зарегистрированных пользователей, используя "@login_required".  

6. **Обработка ошибок**:  
   - Добавить пользовательскую страницу 404 (используйте `handler404` в `urls.py`).  

#### **Дополнительные задания**  
- Добавьте разбиение на страницы в представлении "post_list".  
- Разрешите пользователям редактировать / удалять записи.  
- Оформите шаблоны с помощью CSS.  

---

**Отправка**:  
- Ссылка на репозиторий GitHub с вашим кодом.  
