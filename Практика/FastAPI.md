### **Код (main.py)**  
```python
from fastapi import FastAPI, Request, Form
from fastapi.responses import HTMLResponse # Как встретится в коде
from fastapi.templating import Jinja2Templates
from pydantic import BaseModel

app = FastAPI()
templates = Jinja2Templates(directory="templates")

# Модель для валидации JSON-данных (POST /api/data)
class UserData(BaseModel):
    name: str
    age: int

# Готовые данные (имитация БД)
db = [
    {"id": 1, "name": "Alice", "age": 25},
    {"id": 2, "name": "Bob", "age": 30},
]

# Главная страница (GET) + форма (POST)
@app.get("/", response_class=HTMLResponse) # import
async def read_root(request: Request):
    return templates.TemplateResponse("index.html", {"request": request, "users": db})

@app.post("/submit-form/")
async def handle_form(
    request: Request,
    name: str = Form(required=True),
    age: int = Form(...), # required=True и ... одно и то же
):
    new_user = {"id": len(db) + 1, "name": name, "age": age}
    db.append(new_user)
    return templates.TemplateResponse(
        "index.html", 
        {"request": request, "users": db, "message": "User added!"}
    )

# Простой API-эндпоинт (GET + POST с JSON)
@app.get("/api/data")
async def get_data():
    return {"users": db}

@app.post("/api/data")
async def add_data(user: UserData):
    new_user = {"id": len(db) + 1, "name": user.name, "age": user.age}
    db.append(new_user)
    return {"status": "ok", "user": new_user}
```

### **Шаблон (templates/index.html)**  
```html
<!DOCTYPE html>
<html>
<head>
    <title>FastAPI Exam Example</title>
</head>
<body>
    <h1>Users</h1>
    {% if message %}
        <p style="color: green;">{{ message }}</p>
    {% endif %}
    <ul>
        {% for user in users %}
            <li>{{ user.name }} (Age: {{ user.age }})</li>
        {% endfor %}
    </ul>

    <h2>Add User</h2>
    <form method="post" action="/submit-form/">
        <input type="text" name="name" placeholder="Name" required>
        <input type="number" name="age" placeholder="Age" required>
        <button type="submit">Submit</button>
    </form>

    <h2>API Endpoints</h2>
    <ul>
        <li><a href="/api/data" target="_blank">GET /api/data</a></li>
    </ul>
</body>
</html>
```

---

### **Разбор кода**  

#### **1. Структура проекта**  
```
project/
├── main.py          # FastAPI-приложение
└── templates/
    └── index.html   # Jinja2-шаблон
```

#### **2. Ключевые компоненты**  

1. **FastAPI и Jinja2**  
   - `Jinja2Templates` подключает шаблоны из папки `templates`.  
   - `@app.get("/")` рендерит HTML через `TemplateResponse`.  

2. **Работа с формами (POST /submit-form/)**  
   - Данные формы (`name`, `age`) валидируются через `Form(...)`.  
   - После добавления пользователя в `db` страница обновляется с сообщением.  

3. **API-эндпоинты (/api/data)**  
   - **GET**: возвращает JSON с данными из `db`.  
   - **POST**: принимает JSON, валидирует через `UserData` (Pydantic), добавляет запись.  

4. **Шаблон (index.html)**  
   - Цикл `{% for %}` выводит список пользователей.  
   - Форма отправляет POST-запрос на `/submit-form/`.  

---

### **Как запустить?**  
1. Установите зависимости:  (уже есть)
   ```bash
   pip install fastapi uvicorn jinja2 python-multipart
   ```  
2. Запустите сервер:  
   ```bash
   uvicorn main:app --reload
   ```  
3. Откройте `http://127.0.0.1:8000/`.  
