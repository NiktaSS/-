## 🔥 **Код (Flask + Jinja2)**  

### **1. Установка Flask**  
```bash
pip install flask
```

### **2. Структура проекта**  
```
.
├── app.py          # Основное приложение Flask
└── templates/     # Шаблоны Jinja2
    ├── form.html  # Форма входа
    └── welcome.html # Приветствие
```

### **3. `app.py` (основной файл)**  
```python
from flask import Flask, render_template, request

app = Flask(__name__)

# "База данных" в памяти (логин: пароль)
fake_db = {
    "alex": "password123",
    "andrey": "qwerty"
}

# Главная страница с формой входа
@app.route("/", methods=["GET", "POST"])
def home():
    if request.method == "POST":
        username = request.form.get("username")
        password = request.form.get("password")

        # Проверяем логин/пароль
        if username in fake_db and fake_db[username] == password:
            return render_template("welcome.html", username=username)
        else:
            error = "Неверный логин или пароль!"
            return render_template("form.html", error=error)

    # GET-запрос: просто показать форму
    return render_template("form.html")

# Простой API-эндпоинт (принимает GET-параметры)
@app.route("/api/greet")
def api_greet():
    username = request.args.get("username", default="Гость")
    message = request.args.get("message", default="Привет!")
    return {
        "status": "success",
        "user": username,
        "message": message.upper()
    }

if __name__ == "__main__":
    app.run(debug=True)
```

---

### **4. Шаблоны Jinja2**  

#### **`templates/form.html` (форма входа)**  
```html
<!DOCTYPE html>
<html>
<head>
    <title>Форма входа</title>
</head>
<body>
    <h1>Введите логин и пароль</h1>
    
    {% if error %}
        <p style="color: red;">{{ error }}</p>
    {% endif %}

    <form method="POST" action="/">
        <input type="text" name="username" placeholder="Логин" required>
        <input type="password" name="password" placeholder="Пароль" required>
        <button type="submit">Войти</button>
    </form>

    <p>Пример API: <a href="/api/greet?username=Andrey&message=Test">/api/greet?username=Andrey&message=Test</a></p>
</body>
</html>
```

#### **`templates/welcome.html` (страница после входа)**  
```html
<!DOCTYPE html>
<html>
<head>
    <title>Добро пожаловать</title>
</head>
<body>
    <h1>Привет, {{ username }}!</h1>
    <p>Вы успешно вошли в систему.</p>
    <a href="/">Вернуться</a>
</body>
</html>
```

---

## 🔍 **Разбор кода**  

### **1. Как работает Flask?**  
- `app = Flask(__name__)` — создает приложение.  
- `@app.route("/")` — определяет, какой код выполнится при заходе на `/`.  

### **2. Обработка формы**  
- `request.method` — проверяем, GET это или POST.  
- `request.form.get("username")` — получаем данные из формы.  
- Если пароль верный — рендерим `welcome.html`, иначе — `form.html` с ошибкой.  

### **3. Простой API (`/api/greet`)**  
- Принимает GET-параметры:  
  - `username` (по умолчанию "Гость").  
  - `message` (по умолчанию "Привет!").  
- Возвращает JSON с данными.  

### **4. Шаблоны Jinja2**  
- `{% if error %}` — условие (показывает ошибку, если она есть).  
- `{{ username }}` — подставляет переменную из Python.  

---

## 🚀 **Как запустить?**  
1. Сохраните код в `app.py` и шаблоны в `templates/`.  
2. Запустите:  
   ```bash
   python app.py
   ```
3. Откройте в браузере:  
   - Форма: `http://127.0.0.1:5000/`  
   - API: `http://127.0.0.1:5000/api/greet?username=Andrey&message=Test`  

---

## 💡 **Почему этот код подходит для экзамена?**  
✅ **Минимализм**: Только Flask (без лишних библиотек).  
✅ **Полный цикл**: Форма → обработка → рендер страницы + API.  
✅ **Без БД**: Данные в памяти (`fake_db`).  
✅ **Jinja2**: Шаблоны для фронта.  