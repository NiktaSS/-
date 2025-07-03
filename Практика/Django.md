### **1. Настройка проекта (если нужно создать с нуля)**  
```bash
django-admin startproject exam_prep
cd exam_prep
python manage.py startapp main
```

---

### **2. Код решения**  

#### **`exam_prep/settings.py`** (добавить приложение в `INSTALLED_APPS`)  
```python
INSTALLED_APPS = [
    ...
    'main',
]
```

#### **`main/views.py`** (основная логика)  
```python
from django.shortcuts import render, redirect
from django.http import JsonResponse # Как встретится в коде
from django.views.decorators.http import require_http_methods # Как встретится в коде
import json # не надо, вроде не используется

# Пример готовых данных (вместо БД)
ITEMS = [
    {"id": 1, "name": "Item 1", "price": 100},
    {"id": 2, "name": "Item 2", "price": 200},
]

@require_http_methods(["GET"]) # если нельзя, то код снизу
def item_list(request):
    """Рендеринг списка items через Jinja2.""" # просто комментарий
    return render(request, 'items.html', {'items': ITEMS})

@require_http_methods(["GET", "POST"])
def add_item(request):
    """Обработка формы (GET/POST).""" # просто комментарий
    if request.method == 'POST':
        # Получаем данные из формы
        name = request.POST.get('name')
        price = request.POST.get('price')
        if name and price:
            new_item = {
                "id": len(ITEMS) + 1,
                "name": name,
                "price": int(price),
            }
            ITEMS.append(new_item)
            return redirect('item_list')  # Редирект после успеха
    return render(request, 'add_item.html')

@require_http_methods(["GET"])
def api_items(request):
    """Простой API-эндпоинт (возвращает JSON).""" # просто комментарий
    return JsonResponse({'items': ITEMS})
```

#### **`main/urls.py`**  
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.item_list, name='item_list'),
    path('add/', views.add_item, name='add_item'),
    path('api/items/', views.api_items, name='api_items'),
]
```

#### **`exam_prep/urls.py`** (главный urls.py)  
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('main.urls')),
]
```

#### **Шаблоны Jinja2**  

**`main/templates/items.html`**  
```html
<!DOCTYPE html>
<html>
<head>
    <title>Items List</title>
</head>
<body>
    <h1>Items</h1>
    <ul>
        {% for item in items %}
            <li>{{ item.name }} - ${{ item.price }}</li>
        {% endfor %}
    </ul>
    <a href="{% url 'add_item' %}">Add New Item</a>
</body>
</html>
```

**`main/templates/add_item.html`**  
```html
<!DOCTYPE html>
<html>
<head>
    <title>Add Item</title>
</head>
<body>
    <h1>Add New Item</h1>
    <form method="post">
        {% csrf_token %}
        <label for="name">Name:</label>
        <input type="text" id="name" name="name" required><br>
        <label for="price">Price:</label>
        <input type="number" id="price" name="price" required><br>
        <button type="submit">Submit</button>
    </form>
</body>
</html>
```

---

### **3. Объяснение кода**  

1. **Обработка запросов**:  
   - `item_list`: Рендерит список items через Jinja2 (GET).  
   - `add_item`: Принимает данные формы (POST), валидирует и добавляет item.  
   - `api_items`: Возвращает данные в JSON (простой API).  

2. **Jinja2**:  
   - Шаблоны используют переменные (`{% for %}`, `{{ item.name }}`).  
   - `{% csrf_token %}` обязателен для POST-форм в Django.  

3. **HTTP-методы**:  
   - Декоратор `@require_http_methods` ограничивает допустимые методы (например, только GET для API).  

4. **Работа без БД**:  
   - Данные хранятся в списке `ITEMS` (имитация БД).  

5. **API + Frontend**:  
   - Эндпоинт `/api/items/` возвращает JSON.  
   - Основные страницы рендерят HTML.  

---

### **4. Проверка работы**  
1. Запустите сервер:  
   ```bash
   python manage.py runserver
   ```
2. Откройте:  
   - http://127.0.0.1:8000/ – список items.  
   - http://127.0.0.1:8000/add/ – форма добавления.  
   - http://127.0.0.1:8000/api/items/ – JSON-ответ.  


#### **`main/views.py`** (без @require_http_methods)  
```python
from django.shortcuts import render, redirect
from django.http import JsonResponse, HttpResponseNotAllowed # Как встретится в коде

# Пример готовых данных (вместо БД)
ITEMS = [
    {"id": 1, "name": "Item 1", "price": 100},
    {"id": 2, "name": "Item 2", "price": 200},
]

def item_list(request):
    """Рендеринг списка items через Jinja2 (только GET).""" # просто комментарий
    return render(request, 'items.html', {'items': ITEMS})

def add_item(request):
    """Обработка формы (GET/POST)."""
    if request.method == 'POST':
        # Получаем данные из формы
        name = request.POST.get('name')
        price = request.POST.get('price')
        if name and price:
            new_item = {
                "id": len(ITEMS) + 1,
                "name": name,
                "price": int(price),
            }
            ITEMS.append(new_item)
            return redirect('item_list') 
    
    return render(request, 'add_item.html')

def api_items(request):
    """Простой API-эндпоинт (только GET).""" # просто комментарий
    return JsonResponse({'items': ITEMS})
```
