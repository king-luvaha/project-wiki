Welcome to this hands-on tutorial! In this guide, you'll learn how to build a note-taking app that supports Markdown formatting, checks for grammar errors, and renders notes into clean HTML — all with a modern Python tech stack. We'll go from project setup to API testing in Postman, breaking everything down step by step.

---

## 🚀 What We’ll Build

A RESTful API that allows users to:

* 📤 Upload a Markdown file
* ✍️ Get grammar suggestions
* 💾 Save the note to disk
* 🌐 Render Markdown as HTML

This project will give you practical experience with:

| Tool/Library                        | Purpose                                                |
| ----------------------------------- | ------------------------------------------------------ |
| **FastAPI**                         | A modern, fast (high-performance) Python web framework |
| **Uvicorn**                         | To run the server                                      |
| **SQLAlchemy**                      | For database handling                                  |
| **SQLite**                          | The database                                           |
| **Markdown parsing**                | To convert Markdown to HTML                            |
| **Grammar checking**                | Using LanguageTool for grammar and spell checking      |
| **aiofiles** + **python-multipart** | For file upload support                                |
| **Postman**                         | For API testing                                        |

---

## 🧰 Prerequisites

Ensure the following are installed:

* Python 3.8+
* pip
* Git
* Java (required for LanguageTool)
* Postman

---

## 📁 Step 1: Set Up the Project Structure

```bash
mkdir markdown-note-api
cd markdown-note-api

# Activate virtual environment:
python -m venv venv

# Mac/Linux:
source venv/bin/activate

# Windows:
venv\Scripts\activate

# Install dependencies
pip install fastapi uvicorn sqlalchemy pydantic markdown language-tool-python aiofiles python-multipart
```

Save to `requirements.txt`:

```
# requirements.txt
fastapi
uvicorn
sqlalchemy
pydantic
markdown
language-tool-python
aiofiles
python-multipart
```

### 📁 Project Structure

```bash
markdown-note-api/
├── app/
│   ├── crud.py
│   ├── database.py
│   ├── main.py
│   ├── models.py
│   ├── schemas.py
│   └── utils.py
├── uploads/                 # for uploaded markdown files
├── requirements.txt
└── README.md
```

---

## 🧱 Step 2: Database Setup

**File: `app/database.py`**

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, declarative_base

SQLALCHEMY_DATABASE_URL = "sqlite:///./notes.db"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)
SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)
Base = declarative_base()
```

---

## 📄 Step 3: Define Note Model

**File: `app/models.py`**

```python
from sqlalchemy import Column, Integer, String, Text
from .database import Base

class Note(Base):
    __tablename__ = "notes"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(100))
    content = Column(Text)
    html_content = Column(Text)
    grammar_issues = Column(Text)
```

---

## 📦 Step 4: Create Pydantic Schemas

**File: `app/schemas.py`**

```python
from pydantic import BaseModel

class NoteCreate(BaseModel):
    title: str

class NoteOut(BaseModel):
    id: int
    title: str
    content: str
    html_content: str
    grammar_issues: str

    class Config:
        from_attributes = True  # use this for Pydantic v2+
```


----

## 🔧 Step 5: Utility Functions

**File: `app/utils.py`**

```python
import markdown
import language_tool_python

def convert_md_to_html(md_content: str) -> str:
    return markdown.markdown(md_content)

def check_grammar(text: str) -> str:
    tool = language_tool_python.LanguageTool('en-US')
    matches = tool.check(text)
    return "\n".join([
        f"{m.ruleId}: {m.message} (suggested: {m.replacements})"
        for m in matches
    ])
```

✅ Ensure you have Java ≥ 17 installed.

---

## 🗃️ Step 6: CRUD Operations

**File: `app/crud.py`**

```python
from sqlalchemy.orm import Session
from . import models

def create_note(db: Session, title: str, content: str, html: str, grammar: str):
    note = models.Note(
        title=title, content=content,
        html_content=html, grammar_issues=grammar
    )
    db.add(note)
    db.commit()
    db.refresh(note)
    return note

def get_notes(db: Session):
    return db.query(models.Note).all()

def get_note(db: Session, note_id: int):
    return db.query(models.Note).filter(models.Note.id == note_id).first()
```

---

## 🚀 Step 7: Main FastAPI App

**File: `app/main.py`**

```python
from fastapi import FastAPI, UploadFile, File, Form, Depends, HTTPException
from sqlalchemy.orm import Session
import os

from . import database, models, crud, utils, schemas

app = FastAPI()
models.Base.metadata.create_all(bind=database.engine)

def get_db():
    db = database.SessionLocal()
    try:
        yield db
    finally:
        db.close()

UPLOAD_DIR = "uploads"
os.makedirs(UPLOAD_DIR, exist_ok=True)

@app.post("/upload/", response_model=schemas.NoteOut)
async def upload_note(
    title: str = Form(...),
    file: UploadFile = File(...),
    db: Session = Depends(get_db)
):
    if not file.filename.endswith(".md"):
        raise HTTPException(status_code=400, detail="Only markdown files allowed")

    content = (await file.read()).decode("utf-8")
    html = utils.convert_md_to_html(content)
    grammar = utils.check_grammar(content)

    note = crud.create_note(db, title=title, content=content, html=html, grammar=grammar)
    return note

@app.get("/notes/", response_model=list[schemas.NoteOut])
def list_notes(db: Session = Depends(get_db)):
    return crud.get_notes(db)

@app.get("/notes/{note_id}", response_model=schemas.NoteOut)
def get_single_note(note_id: int, db: Session = Depends(get_db)):
    note = crud.get_note(db, note_id)
    if not note:
        raise HTTPException(status_code=404, detail="Note not found")
    return note
```

---

## 🧪 Step 8: Run the API Server

```bash
uvicorn app.main:app --reload
```

Visit:
👉 [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
to access FastAPI’s auto-generated documentation.

---

## 🔍 Step 9: Test with Postman

### 🚀 POST `/upload/`

* **Method**: POST
* **URL**: `http://127.0.0.1:8000/upload/`
* **Body**: `form-data`

  * `title`: (Text) My First Note
  * `file`: (File) Choose a `.md` file

### 📄 GET `/notes/`

* Lists all notes

### 📄 GET `/notes/{id}`

* Retrieve a specific note by ID

---
## 🛠️ Step 10a: Troubleshooting

### ❗ Error: `ModuleNotFoundError: No java install detected`

✅ Fix: Install Java ≥ 17 from [https://adoptium.net](https://adoptium.net)

### ❗ Error: `SystemError: Detected java 1.8`

✅ Fix: Uninstall Java 8, install Java 17 or 21

---


## ✅ Step 10b: Solution - Install Java

### 🔹 Step 1: Download Java

1. Go to: [https://www.oracle.com/java/technologies/javase-downloads.html](https://www.oracle.com/java/technologies/javase-downloads.html)
   or directly use OpenJDK from: [https://adoptium.net/en-GB/temurin/releases](https://adoptium.net/en-GB/temurin/releases)

2. Download the **latest LTS version** (e.g. Java 17 or Java 21) for Windows.

3. Choose the **.msi (installer)** version for easy setup.

---

### 🔹 Step 2: Install Java

* Run the installer
* Allow it to set the `JAVA_HOME` and `PATH` environment variables (the installer should do this automatically).

---

### 🔹 Step 3: Verify Installation

Open a new terminal (PowerShell or CMD) and run:

```bash
java -version
```

You should see something like:

```bash
java version "17.0.x" 2024-xx-xx
Java(TM) SE Runtime Environment (build 17.0.x+xx)
```

---

### 🔹 Step 4: Restart Uvicorn Server

After Java is installed:

```bash
uvicorn app.main:app --reload
```

Now, when you upload a markdown file via Postman, it should **successfully return the grammar-checked note** instead of a 500 error.

---

## 📌 Optional Improvements

* [ ] Add basic web UI to view notes (HTML rendering)
* [ ] Add login/authentication
* [ ] Use cloud grammar API instead of Java
* [ ] Allow editing/deleting notes

---

