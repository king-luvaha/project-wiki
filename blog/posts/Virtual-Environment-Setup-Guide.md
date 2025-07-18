---
slug: python-venv-vs-virtualenvwrapper-complete-setup-guide
title: "🧠 Python Virtual Environments: venv vs virtualenv vs wrapper"
authors: luvaha
tags:
  - Python
  - python-tutorial
  - venv
  - virtualenv
  - virtualenvwrapper
  - developer-tools
date: 2025-07-10
---
---

If you’re a Python developer, you’ve likely encountered the need to isolate dependencies for each project. That’s where **virtual environments** come in. But with tools like `venv`, `virtualenv`, and `virtualenvwrapper` floating around, which one should you use?

In this post, we’ll explain what each tool does, when to use it, and provide platform-specific setup instructions for **Windows**, **Linux**, and **macOS**.

---

<!--truncate-->

## 🐍 What Is a Virtual Environment?

A **virtual environment** is a self-contained directory that contains a Python interpreter and all the packages your project needs — isolated from your system-wide Python.

Without one, installing packages with `pip` would affect your entire system and other projects.

---

## ⚙️ Tool Overview and Purpose

### ✅ `venv` — Python’s Built-in Virtual Environment Module

- **Built-in** since Python 3.3+
- Lightweight and easy to use
- Great for beginners or simple projects

📦 Creates isolated environments with their own Python binary and `site-packages`.

---

### ✅ `virtualenv` — A More Powerful Alternative

- External tool; install via `pip`
- Compatible with Python 2 and early Python 3
- More features and speed than `venv`
- Allows you to specify a Python interpreter

📦 Creates environments similarly to `venv`, but with greater flexibility and support for legacy Python.

---

### ✅ `virtualenvwrapper` — A Productivity Booster for `virtualenv`

- A set of shell scripts (for Unix) or CLI tools (for Windows)
- Adds commands like `mkvirtualenv`, `workon`, `rmvirtualenv`
- Centralizes all environments in a single directory

📦 Ideal for developers managing many Python projects and switching between them often.

---

## 🌳 Decision Tree: Which One Should You Use?

```
Are you using Python 3.3+?
│
├── No → Use `virtualenv`
│
└── Yes
    │
    ├── Want something built-in and simple? → Use `venv`
    │
    └── Do you manage many projects and want shell commands for easy switching?
        └── Yes → Use `virtualenvwrapper`
```

---

## 🔧 Platform-Specific Setup Instructions

Let’s break it down by platform: **Windows**, **Linux**, and **macOS**.

---

### 🪟 WINDOWS

#### 🔹 Using `venv` on Windows

```bash
# Create environment
python -m venv myenv

# Activate (Command Prompt)
myenv\Scripts\activate.bat

# Activate (PowerShell)
myenv\Scripts\Activate.ps1

# Deactivate
deactivate
```

#### 🔹 Using `virtualenv` on Windows

```bash
pip install virtualenv

# Create environment
virtualenv myenv

# Activate (Command Prompt)
myenv\Scripts\activate.bat

# Activate (PowerShell)
myenv\Scripts\Activate.ps1

# Deactivate
deactivate
```

#### 🔹 Using `virtualenvwrapper-win`

Install the Windows-compatible wrapper:

```bash
pip install virtualenvwrapper-win
```

Usage:

```bash
mkvirtualenv myenv
workon myenv
rmvirtualenv myenv
deactivate
```

💡 By default, environments are stored in `%USERPROFILE%\Envs`.

---

### 🐧 LINUX (Ubuntu/Debian-based)

#### 🔹 Using `venv` on Linux

```bash
sudo apt install python3-venv

# Create environment
python3 -m venv myenv

# Activate
source myenv/bin/activate

# Deactivate
deactivate
```

#### 🔹 Using `virtualenv` on Linux

```bash
pip install virtualenv

# Create environment
virtualenv myenv

# Activate
source myenv/bin/activate

# Deactivate
deactivate
```

#### 🔹 Using `virtualenvwrapper` on Linux

```bash
pip install virtualenv virtualenvwrapper

# Add to ~/.bashrc or ~/.zshrc
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=$(which python3)
source $(which virtualenvwrapper.sh)

# Apply changes
source ~/.bashrc  # or source ~/.zshrc
```

Then:

```bash
mkvirtualenv myenv
workon myenv
rmvirtualenv myenv
deactivate
```

---

### 🍏 macOS

#### 🔹 Using `venv` on macOS

```bash
# Use built-in venv
python3 -m venv myenv

# Activate
source myenv/bin/activate

# Deactivate
deactivate
```

#### 🔹 Using `virtualenv` on macOS

```bash
pip3 install virtualenv

# Create environment
virtualenv myenv

# Activate
source myenv/bin/activate

# Deactivate
deactivate
```

#### 🔹 Using `virtualenvwrapper` on macOS

```bash
pip3 install virtualenv virtualenvwrapper

# Add to ~/.zshrc or ~/.bash_profile
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=$(which python3)
source $(which virtualenvwrapper.sh)

# Apply changes
source ~/.zshrc  # or ~/.bash_profile
```

Then:

```bash
mkvirtualenv myenv
workon myenv
rmvirtualenv myenv
deactivate
```

---

## 🧪 Common `virtualenvwrapper` Commands

Here are the commands you’ll use daily:

| Command              | Description                                    |
| -------------------- | ---------------------------------------------- |
| `mkvirtualenv myenv` | Create a new virtual environment named `myenv` |
| `workon`             | List all environments                          |
| `workon myenv`       | Activate the `myenv` environment               |
| `deactivate`         | Exit the current environment                   |
| `rmvirtualenv myenv` | Delete the environment named `myenv`           |

---

## 📊 Feature Comparison Table

|Feature|`venv`|`virtualenv`|`virtualenvwrapper`|
|---|---|---|---|
|Built-in (Python 3.3+)|✅|❌|❌|
|Supports Python 2|❌|✅|✅|
|Lightweight|✅|✅|⚠️ Shell-dependent|
|Easy switching|❌|❌|✅|
|Centralized env storage|❌|❌|✅|
|Custom automation hooks|❌|⚠️ Limited|✅|
|Ideal for many projects|❌|⚠️|✅|
|Great for beginners|✅|✅|⚠️ Slight learning curve|

---

## 🧼 Summary: When to Use What?

|Use Case|Recommended Tool|
|---|---|
|New to Python|`venv`|
|Legacy Python or cross-version use|`virtualenv`|
|Managing many projects|`virtualenvwrapper`|
|IDE integration (VS Code, PyCharm)|`venv`|
|Need custom activate hooks|`virtualenvwrapper`|

---

## ✅ Conclusion

- Start with `venv` for simplicity and speed.
- Use `virtualenv` if you're dealing with legacy versions.
- Upgrade to `virtualenvwrapper` if you're managing **multiple projects** and want **productivity tools** like `workon`, `rmvirtualenv`, and centralized management.
