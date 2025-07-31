---
slug: setup-django-project-from-scratch
title: How to Set Up a Django Project from Scratch (Step-by-Step Guide)
authors: luvaha
tags:
  - Django
  - Python
  - Backend
date: 2025-07-31
---
---
Here’s a full blog post you can use or customize to teach others how to set up a Django project from scratch:

---

# 🚀 How to Set Up a Django Project from Scratch (Step-by-Step Guide)

Whether you're a beginner or revisiting Django after a break, setting up your project correctly from the beginning saves time and future headaches. In this post, I’ll guide you through the **complete setup process of a Django project** from scratch—step by step.

---

## ✅ Prerequisites

Before we begin, make sure you have:

* Python 3.8 or above installed
* `pip` (Python package manager)
* Git Bash, Terminal, or Command Prompt
* Basic understanding of Python
* VS Code (optional but recommended)

---

## 🛠 Step 1: Create a Virtual Environment

Creating a virtual environment ensures your project dependencies are isolated.

```bash
# Create a new project directory
mkdir my_django_project
cd my_django_project

# Create a virtual environment
python -m venv venv

# Activate it
# On Windows:
venv\Scripts\activate

# On Mac/Linux:
source venv/bin/activate
```

Once activated, your terminal should show `(venv)`.

---

## 📦 Step 2: Install Django

With the virtual environment activated, install Django:

```bash
pip install django
```

Check if Django was installed correctly:

```bash
django-admin --version
```

---

## 🧱 Step 3: Create the Django Project

Now let’s create the actual Django project.

```bash
django-admin startproject config .
```

> `config` is just a common name for the root Django project (you can name it anything). The dot (`.`) tells Django to place files in the current folder.

Your structure now looks like:

```
my_django_project/
├── manage.py
├── config/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
```

---

## 🔍 Step 4: Understand the Project Structure

* `manage.py`: Entry point to manage your app (run server, migrations, etc.)
* `config/settings.py`: All your configuration (DB, installed apps, etc.)
* `config/urls.py`: App routes are defined here
* `config/wsgi.py`: Used for deployment on production servers

---

## 🖥 Step 5: Run the Development Server

Let’s test if everything works.

```bash
python manage.py runserver
```

Open your browser and go to:

```
http://127.0.0.1:8000/
```

You should see the Django welcome page 🎉

---

## 🧩 Step 6: Create a Django App

Apps are where you build features (e.g., blog, users, products).

```bash
python manage.py startapp myapp
```

Update `config/settings.py` to register the app:

```python
INSTALLED_APPS = [
    ...
    'myapp',
]
```

---

## 🔐 Step 7: Set Up the Admin Panel

Create a superuser:

```bash
python manage.py createsuperuser
```

Follow the prompts and create credentials.

Then go to:

```
http://127.0.0.1:8000/admin
```

Login using the superuser credentials you just created. You now have access to Django’s powerful admin panel!

---

## 🧪 Bonus: Make Your First Migration

To prepare the database for use, run:

```bash
python manage.py migrate
```

This creates the necessary tables (users, sessions, etc.).

---

## 🎯 Final Tips

* Keep your project in Git from day one
* Use `.env` files for secrets in production
* Use Django’s `runserver` only for development

---

## 🚧 Sample Folder Structure (After App Creation)

```
my_django_project/
├── config/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── myapp/
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── venv/
```

---

## 📘 Conclusion

Setting up a Django project is the first step to building powerful web applications with Python. From here, you can start adding views, templates, models, and more.