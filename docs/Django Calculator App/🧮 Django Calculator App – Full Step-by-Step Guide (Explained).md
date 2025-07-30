
This project builds a simple yet functional web-based calculator using **Django** and **Bootstrap**. It evaluates mathematical expressions entered by the user and returns either the result or an error message.

---

## ✅ Step 1: Create Project Directory

We begin by organizing our workspace.

```bash
mkdir django-calculator-app
cd django-calculator-app
```

> 📁 This folder will hold the entire Django project, virtual environment, and all your source files.

---

## ✅ Step 2: Set Up a Virtual Environment

Virtual environments keep your project dependencies isolated.

```bash
python -m venv venv
```

Activate the virtual environment:

```bash
venv/Scripts/activate   # Windows
```

> ✅ After activation, any Python packages you install (like Django) will only affect this project.

---

## ✅ Step 3: Initialize Project and App

### (Optional) Initialize Git

```bash
git init
```

> 🗃️ Git helps you track changes and collaborate.

### Install Django in the environment

```bash
pip install django
```

> 📦 This installs Django only for this virtual environment.

### Create Django Project

```bash
django-admin startproject djangoprojects
cd djangoprojects
```

> 📂 This creates a project folder `djangoprojects/` with key files like `settings.py`, `urls.py`, and `manage.py`.

### Run the Server

```bash
python manage.py runserver
```

> ✅ This confirms Django is installed correctly. Visit `http://127.0.0.1:8000` in your browser to see the welcome page.

### Create the App

```bash
python manage.py startapp calculatorapp
```

> 🔧 Django apps are components of a project. We’ll place all calculator logic here.

---

## ✅ Step 4: Apply Migrations

```bash
python manage.py migrate
```

> 🧱 This sets up the initial database tables Django needs to run (like for users and sessions).

---

## ✅ Step 5: Configure Template and Static File Paths

In `djangoprojects/settings.py`, update:

```python
# Tell Django where to find HTML templates
'DIRS': [os.path.join(BASE_DIR, 'templates')],
```

And:

```python
# Tell Django where to find static files (e.g., images, CSS)
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
```

> 📁 These settings allow you to store your HTML and CSS outside of app folders, for better organization.

### 🔹 Add Static Files

#### Create `static/` and add image

Example:

```
django-calculator-app/
└── djangoprojects/
    └── static/
        └── main.jpg
```


---

## ✅ Step 6: Create Template and Static Folders

Inside the root of your project (`django-calculator-app/djangoprojects/`):

```bash
mkdir templates
mkdir static
```

> 🏗️ This is where we’ll store `index.html` and static assets like images.

---

## ✅ Step 7: Add App Routes

In `djangoprojects/djangoprojects/urls.py`, add:

```python
from django.urls import path, include

urlpatterns = [
    path('calculatorapp/', include('calculatorapp.urls')),
]
```

> 🌐 This connects the project’s main URLs to your app’s URLs.

---

## ✅ Step 8: Create App Routes and Views

Create `calculatorapp/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

In `calculatorapp/views.py`:

```python
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    return HttpResponse("Server started")
```

> 🧠 When you visit `http://127.0.0.1:8000/calculatorapp/`, this view returns a simple message.

---

## ✅ Step 9: Add a Root App (Optional but Useful)

Create a homepage app to avoid a 404 error when visiting `/`:

```bash
python manage.py startapp rootapp
```

Update `rootapp/views.py`:

```python
def index(request):
    return HttpResponse("Server started")
```

Create `rootapp/urls.py`:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='root'),
]
```

Update  `djangoprojects/urls.py` :

```python
from django.urls import path, include

urlpatterns = [
    path('', include('rootapp.urls')), # Add this line
    path('calculatorapp/', include('calculatorapp.urls')),
]
```

> ✅ This shows the homepage message when visiting the root URL.

---

## ✅ Step 10: Configure Templates Folder for Calculator App

In `settings.py` under `TEMPLATES`, update:

```python
'DIRS': [os.path.join(BASE_DIR, 'templates/calculatorapp')],
```

Now, create:

```bash
mkdir templates/calculatorapp
touch templates/calculatorapp/index.html
```

---

## ✅ Step 11: Build the Frontend UI (Bootstrap + Form)

First, update `calculatorapp/views.py`:

```python
from django.shortcuts import render

def index(request):
    return render(request, 'index.html')
```

Then, in `templates/calculatorapp/index.html`, paste a Bootstrap starter with a form:

```django
{% load static %}
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Django Calculator App</title>
    <link
      href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.7/dist/css/bootstrap.min.css"
      rel="stylesheet"
    />
  </head>
  <body>
    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg bg-body-tertiary sticky-top">
      <div class="container-fluid">
        <a class="navbar-brand" href="#">Django Calculator App</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse"
          data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent"
          aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item"><a class="nav-link" href="#about">About</a></li>
            <li class="nav-item"><a class="nav-link" href="#instructions">Instructions</a></li>
          </ul>
        </div>
      </div>
    </nav>

    <!-- Calculator Form -->
    <main class="container-fluid flex-grow-1" style="margin-bottom: 20rem;">
      <div class="row">
        <div class="col-md-6" style="margin: 1rem auto 0 auto;">
          <img class="img-fluid" src="{% static 'main.jpg' %}" alt="Calculator">
          <div class="card mt-3">
            <div class="card-body">
              <form method="GET" action="#">
                <div class="mb-3">
                  <input type="search" class="form-control" name="query" id="query" placeholder="Enter expression">
                </div>
                <div class="row">
                  <div class="col-6">
                    <input type="submit" class="btn btn-success w-100" value="Calculate">
                  </div>
                  <div class="col-6">
                    <input type="reset" class="btn btn-danger w-100" value="Clear">
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>

      <!-- About Section -->
      <div class="row">
        <div class="col-md-6 mt-5 mx-auto text-center">
          <h2 id="about">About</h2>
          <hr>
          <p class="lead">Our calculator app performs basic mathematical operations securely and efficiently.</p>
        </div>
      </div>

      <!-- Instructions Section -->
      <div class="row">
        <div class="col-md-6 mt-5 mx-auto text-center">
          <h2 id="instructions">Instructions</h2>
          <hr>
          <ul class="list-group text-start">
            <li class="list-group-item">+ Addition</li>
            <li class="list-group-item">- Subtraction</li>
            <li class="list-group-item">* Multiplication</li>
            <li class="list-group-item">/ Division</li>
            <li class="list-group-item">// Floor Division</li>
            <li class="list-group-item">** Exponential</li>
            <li class="list-group-item">% Modulo</li>
            <li class="list-group-item">( ) Parentheses</li>
          </ul>
        </div>
      </div>
    </main>

    <!-- Footer -->
    <footer class="bg-dark text-white py-3 text-center">
      <div class="container-fluid">
        <p class="mb-0">&copy; 2025 Django Calculator App. All rights reserved.</p>
      </div>
    </footer>

    <!-- Bootstrap JS -->
    <script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.11.8/dist/umd/popper.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.7/dist/js/bootstrap.min.js"></script>
  </body>
</html>
```

Added a navbar, content section, and a result display area for better design.

---

## ✅ Step 12: Implement the Backend Logic (Evaluating Math)

### Update `calculatorapp/urls.py`

Add a new path:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('submitquery', views.submitquery, name='submitquery'),  # New path
]
```

### Update `calculatorapp/views.py`

Add this below `index()`:

```python
def submitquery(request):
    q = request.GET['query']
    try:
        ans = eval(q)
        mydictionary = {
            "q": q,
            "ans": ans,
            "error": False,
            "result": True
        }
    except:
        mydictionary = {
            "error": True,
            "result": False
        }
    return render(request, 'index.html', context=mydictionary)
```

### 🔹 Update HTML Form Action in `index.html`

Find the `<form>` tag and update the action:

```django
<form method="GET" action="{% url 'submitquery' %}">
```

This will route the submitted form data to the `submitquery` view you just defined.



> **Note:** `eval()` can be dangerous if user input is not properly validated. In production apps, use a safer expression parser like `ast.literal_eval` or third-party libraries like `NumExpr`.


---

## ✅ Step 13: Add Conditional Display of Results

Inside `index.html`, add:

```django
{% if error %}
  <div class="row"> 
    <div class="col-md-6" style="margin: 0 auto;">
      <div class="alert alert-danger alert-dismissible fade show" role="alert">
        <strong>Error!</strong> Please enter a valid expression.
        <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
      </div>
    </div>
  </div>
{% endif %}

{% if result %}
  <div class="row">
    <div class="col-md-6" style="margin: 0 auto;">
      <div class="alert alert-success alert-dismissible fade show" role="alert">
        <strong>{{ q }} = {{ ans }}</strong>
        <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
      </div>
    </div>
  </div>
{% endif %}
```

These blocks will dynamically render success or error messages based on the backend logic.

---

## ✅ You’re Done! 🎉

### What you’ve learned:

- Django project and app structure
- Virtual environments
- Templates and static file setup
- Handling form input via GET requests
- Displaying results and errors dynamically
- Basic Bootstrap UI

---

## 🚀 What’s Next?

You could:
- Improve input validation
- Add logs or calculation history
- Use `POST` instead of `GET` for form submission
- Deploy the app to a platform like [PythonAnywhere](https://www.pythonanywhere.com) or [Render](https://render.com)