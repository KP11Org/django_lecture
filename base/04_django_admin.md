**Лекция: Освоение Django admin.py**  
**Продолжительность**: 60-75 минут  

---

### **1. Знакомство с Django Admin**  
**Что такое Django Admin?**  
- Встроенный, автоматически генерируемый пользовательский интерфейс для управления содержимым базы данных.  
- Позволяет администраторам выполнять операции **CRUD** (создание, чтение, обновление, удаление) записей.  
- Настраивается с помощью `admin.py` в ваших приложениях Django.  

**Зачем использовать Django Admin?**  
- Быстрое управление бэкендом без написания пользовательских представлений.  
- Безопасный и удобный для пользователей, не имеющих технического образования.  
- Расширяемый с помощью дополнительных функций, таких как фильтры, поиск и действия.  

---

### **2. Базовая настройка**  
#### **Регистрация моделей**  
1. **Простая регистрация**:  
   ```python  
   # app/admin.py  
   from django.contrib import admin  
   from .models import Book  

   admin.site.register(Book)  # Теперь видно в админке!  
   ```  
2. **Использование декораторов**:  
   ```python  
   @admin.register(Book)  
   class BookAdmin(admin.ModelAdmin):  
       pass  
   ```  

#### **Создание суперпользователя**  
```bash  
python manage.py createsuperuser  
```  
Перейдите по адресу `http://localhost:8000/admin`, чтобы войти в систему.  

---

### **3. Настройка интерфейса администратора**  
#### **Общие настройки**  
1. **`list_display`**: отображение определенных полей в представлении списка.  
   ```python  
   class BookAdmin(admin.ModelAdmin):  
       list_display = ('title', 'author', 'published_date', 'is_published')     
   ```  
2. **`list_filter`**: добавление фильтров для определенных полей.  
   ```python  
   list_filter = ('is_published', 'author')  
   ```  
3. **`search_fields`**: включить поиск по текстовым полям.  
   ```python  
   search_fields = ('title', 'author__name')  # Используйте "__" для связанных полей  
   ```  
4. **`fieldsets`**: организовать поля в группы в форме редактирования.  
   ```python  
   fieldsets = (  
       ('Basic Info', {'fields': ('title', 'author')}),  
       ('Details', {'fields': ('is_published', 'published_date')}),  
   )   
   ```  

#### **Пример**  
```python  
@admin.register(Book)  
class BookAdmin(admin.ModelAdmin):  
    list_display = ('title', 'author', 'is_published')  
    list_filter = ('is_published',)  
    search_fields = ('title', 'author__name')  
    fieldsets = (  
        (None, {'fields': ('title', 'author')}),  
        ('Status', {'fields': ('is_published',)}),  
    )  
```    

---

### **4. Расширенные функции**  
#### **Вложенные модели (связанные модели)**  
- Редактируйте связанные модели на той же странице, что и родительскую модель.  
```python  
class ChapterInline(admin.TabularInline):  # or StackedInline  
    model = Chapter  
    extra = 1  # Number of empty forms to display  

class BookAdmin(admin.ModelAdmin):  
    inlines = [ChapterInline]  
```  

#### **Пользовательские действия**  
- Определите массовые действия для выбранных записей.  
```python  
@admin.action(description='Mark selected books as published')  
def publish_books(modeladmin, request, queryset):  
    queryset.update(is_published=True)  

class BookAdmin(admin.ModelAdmin):  
    actions = [publish_books]  
```   

#### **Настройка сайта администратора**  
1. **Изменить заголовок администратора**:  
   ```python  
   # project/admin.py  
   admin.site.site_header = "My Library Admin"  
   ```  
2. **Изменить шаблоны**:  
   - Создайте `templates/admin/base_site.html`, чтобы изменить внешний вид.  

---

### **5. Безопасность и лучшие практики**  
1. **Разрешения**:  
   - Используйте встроенные в Django разрешения (просмотр, добавление, изменение, удаление).  
   - Ограничьте доступ с помощью групп и разрешений пользователей.  
2. **Поля только для чтения**:  
   ```python  
   readonly_fields = ('created_at',)  
   ```  
3. **Не раскрывайте конфиденциальные данные**:  
   - Никогда не включайте поля с паролями в `list_display`!  

---

### **Домашнее задание**  
**Цель**: создать **систему управления библиотекой** с индивидуальным интерфейсом администратора.  

#### **Задачи**  
1. **Настройка**:  
   - Создайте проект Django `library` и приложение `books`.  
   - Определите модель `Book` с полями: `название`, `автор`, `опубликовано`, `дата публикации`.  
   - Добавьте модель `Chapter` с внешним ключом `Book`.  

2. **Настройка администратора**:  
   - Зарегистрируйте обе модели в `admin.py`.  
   - Настройте `BookAdmin` следующим образом:  
     - Отображайте `title`, `author` и `is_published` в представлении списка.  
     - Добавьте фильтр для `is_published`.  
     - Добавьте `Chapter` в качестве встроенного элемента.  
   - Создайте пользовательское действие для отмены публикации книг.  

3. **Безопасность**:  
   - Убедитесь, что `дата публикации` доступна только для чтения в админке.  

4. **Бонус**:  
   - Настройте заголовок и шапку админ-сайта.  
   - Добавьте панель поиска книг по `названию` и `автору`.  

#### **Отправка**:   
- Поделитесь ссылкой на свой репозиторий GitHub.  
- Приложите скриншоты вашего пользовательского интерфейса администратора.  
