**Лекция: Фреймворк сообщений Django**  

---

### **Цель**  
К концу этой лекции студенты смогут:  
1. Понять назначение фреймворка сообщений Django.  
2. Добавлять и отображать временные сообщения (оповещения/нотификации) в приложениях Django.  
3. Настройте типы и стиль сообщений.  
4. Эффективно используйте сообщения в процессах аутентификации и отправки форм.  

---

### **1. Введение в сообщения Django**  
Платформа **messages framework** позволяет отправлять временные разовые уведомления пользователям после определенных действий (например, отправки формы, входа в систему/выхода из системы). Сообщения сохраняются в файлах cookie или сеансах и отображаются один раз перед удалением.  

**Общие варианты использования**:  
- Сообщения об успешном завершении после отправки формы.  
- Предупреждения об ошибках при неверном вводе.  
- Информационные сообщения/ предупреждения о действиях пользователя.  

**Типы сообщений по умолчанию**:  
- `DEBUG` (только для разработки)  
- `INFO`, `SUCCESS`, `WARNING`, `ERROR`  

---

### **2. Конфигурация**  
#### Шаг 1: Убедитесь, что промежуточное программное обеспечение и контекстные процессоры  
В `settings.py` убедитесь, что они включены:  
```python  
MIDDLEWARE = [  
    ...  
    'django.contrib.sessions.middleware.SessionMiddleware',  
    'django.contrib.messages.middleware.MessageMiddleware',  # Required  
    ...  
]  

TEMPLATES = [  
    {  
        'OPTIONS': {  
            'context_processors': [  
                ...  
                'django.contrib.messages.context_processors.messages',  # Required  
            ],  
        },  
    },  
]  
``` 

#### Шаг 2: Добавьте оповещения Bootstrap (необязательно)  
Чтобы оформить сообщения с помощью Bootstrap, включите Bootstrap CSS/JS в свой базовый шаблон.  

---

### **3. Добавление сообщений в представления**  
Используйте модуль "Сообщения" Django для добавления сообщений в представления.  

#### Пример: Сообщение об успешной отправке формы  
```python  
from django.contrib import messages  
from django.shortcuts import render, redirect  

def contact_view(request):  
    if request.method == 'POST':  
        # Process form data  
        messages.success(request, 'Your message was sent successfully!')  
        return redirect('home')  
    return render(request, 'contact.html')  
```    

**Доступные методы**:  
- `messages.debug()`, `messages.info()`, `messages.success()`, `messages.warning()`, `messages.error()`.  

---

### **4. Отображение сообщений в шаблонах**  
Циклически просматривайте сообщения в шаблоне (например, `base.html`):  
```html  
{% if messages %}  
  {% for message in messages %}  
    <div class="alert alert-{{ message.tags }} alert-dismissible fade show" role="alert">  
      {{ message }}  
      <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>  
    </div>  
  {% endfor %}  
{% endif %}  
``` 

**Ключевые замечания**:  
- `message.tags" соответствует типу сообщения (например, "success`, `error`).  
- Используйте классы начальной загрузки (`alert-success`, `alert-danger`) для стилизации.  

---

### **5. Настройка сообщений**  
#### Пользовательские теги и фильтры  
Создайте пользовательский уровень сообщений в `settings.py`:  
```python  
from django.contrib.messages import constants as messages_constants  

MESSAGE_TAGS = {  
    messages_constants.SUCCESS: 'success',  
    messages_constants.ERROR: 'danger',  # Aligns with Bootstrap's 'danger' class  
    50: 'custom-tag',  # Custom level (e.g., CRITICAL = 50)  
}  
```  

#### Добавить пользовательские сообщения  
```python  
messages.add_message(request, messages_constants.CRITICAL, 'Critical error!')  
```    

---

### **6. Лучшие практики**  
- **Безопасность**: Никогда не включайте конфиденциальные данные в сообщения.  
- **Удобство для пользователя**:  
  - Используйте "SUCCESS" для успешных действий (например, отправки формы).  
  - Используйте "ERROR" для ошибок проверки.  
  - Делайте сообщения краткими и действенными.  
- **Тестирование**: Проверка сообщений в модульных тестах с использованием self.assertContains(response, "Ваше сообщение").  

---

### **Домашнее задание**  

#### **Часть 1: Базовая интеграция сообщений**  
1. Создайте `Контактную форму` с представлением и шаблоном.  
2. Добавьте сообщение об успешном завершении после отправки формы.  
3. Отображайте сообщения с помощью Bootstrap alerts в своем шаблоне.  

#### **Часть 2. Сообщения для проверки подлинности**  
1. Добавьте сообщения для:  
   - Успешного входа в систему (`SUCCESS`).  
   - Неудачный вход в систему (`ERROR`).  
   - Подтверждение выхода из системы (`INFO`).  
2. Обновите `login.html` и `base.html" для отображения этих сообщений.  

#### **Часть 3 (расширенная): Настраиваемые уровни сообщений.**  
1. Задайте пользовательский уровень сообщения (например, "VIP = 60") в поле `settings.py`.  
2. Используйте этот уровень для отображения стилизованного сообщения (например, о специальных предложениях для VIP-пользователей).  

---

### **Рекомендации по отправке**  
- Отправьте репозиторий на GitHub с:  
  - Проектом на Django, в котором реализованы все части домашней работы.  
  - `README.md`, Скриншоты сообщений в действии.  


---

### **Ресурсы**  
- [Документация по сообщениям Django](https://docs.djangoproject.com/en/4.2/ref/contrib/messages/)  
- [Bootstrap Alerts](https://getbootstrap.com/docs/5.3/components/alerts/)  

