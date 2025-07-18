Here’s a clear plan to build your **CRUD To-do List RESTful API** in Python, using **FastAPI** for performance and simplicity. This guide covers all required and bonus features.

---

## 🔧 Tech Stack

* **Python**
* **FastAPI** – Web framework
* **SQLAlchemy** – ORM
* **SQLite/PostgreSQL** – Database
* **Pydantic** – Data validation
* **JWT (PyJWT)** – Token-based authentication
* **Uvicorn** – ASGI server
* **Alembic** – DB migrations (optional)
* **Redis** – (for rate limiting or caching, optional)
* **pytest** – Unit testing

---

## 📁 Project Structure

```
todo_api/
├── app/
│   ├── routes/
│   │   ├── todo.py
│   │   └── user.py
│   ├── auth.py
│   ├── config.py
│   ├── database.py
│   ├── dependencies.py
│   ├── main.py
│   ├── models.py
│   └── schemas.py
├── venv/
├── .env
├── README.md
├── /gitignore
└── todo.db
```

---

## ✅ Features Breakdown

### 1. 🔐 User Registration & Authentication

* `/register` – Create a new user
* `/login` – Return access & refresh JWTs
* `/refresh-token` – Generate new access token

**Security**

* Password hashing (bcrypt)
* JWT for access control
* Token expiry, blacklisting (for logout if needed)

### 2. 📋 To-do CRUD Endpoints

* `POST /todos/` – Create task
* `GET /todos/` – List (with pagination, filters)
* `GET /todos/{id}` – Retrieve
* `PUT /todos/{id}` – Update
* `DELETE /todos/{id}` – Delete

### 3. 🔒 Authenticated Access Only

* All `/todos/*` endpoints protected by JWT

### 4. ✅ Validation & Error Handling

* Pydantic for strict request/response validation
* Custom exception handlers for 401, 404, etc.

### 5. 📃 Pagination & Filtering

* `GET /todos/?status=done&sort=created_at&limit=10&offset=0`

### 6. 🚀 Bonus Features

* Unit Tests with `pytest`
* Rate limiting using a simple decorator or Redis (e.g., `slowapi`)
* Refresh token mechanism with `/refresh-token`


---

## Step 0a: Create `.env` File
Create a `.env` file in the project root:

```
# .env

# JWT secrets
SECRET_KEY=supersecretaccesskey
REFRESH_SECRET_KEY=supersecretrefreshkey
ALGORITHM=HS256

# Token expiry
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

# Database URL
DATABASE_URL=sqlite:///./todo.db

```


---

## Step 0b: Create file for managing config:

📄 `app/config.py` — new file for managing config:

``` python
from pydantic_settings import BaseSettings
from dotenv import load_dotenv
import os

# 👇 Load the .env file from the correct path
load_dotenv(dotenv_path=os.path.join(os.path.dirname(__file__), '.env'))

class Settings(BaseSettings):
    SECRET_KEY: str
    REFRESH_SECRET_KEY: str
    ALGORITHM: str = "HS256"
    ACCESS_TOKEN_EXPIRE_MINUTES: int
    REFRESH_TOKEN_EXPIRE_DAYS: int
    DATABASE_URL: str

    class Config:
        env_file = ".env"  # Not required with load_dotenv but still good

settings = Settings()

```


---

## Step 1: Set up the base project and install dependencies

Create a virtual environment and install the required packages:

```bash
python -m venv venv
source venv/bin/activate  # On Windows use: venv\Scripts\activate

pip install fastapi uvicorn sqlalchemy pydantic python-jose[cryptography] passlib[bcrypt] python-dotenv
pip install pydantic-settings
 pip install pydantic[email]  
```

Optional (for testing, rate limiting, etc.):

```bash
pip install pytest httpx slowapi
```

---

### 📁 Folder structure after Step 1

```
todo_api/
├── app/
│   └── main.py  ← (we start here)
├── venv/
├── .env
├── requirements.txt
```

---

## Step 2: `main.py`

This is the entry point of your FastAPI app.

#### `app/main.py`

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(title="To-Do List API")

# CORS Middleware (if using frontend or testing from browser tools)
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # For development only; restrict in production
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

@app.get("/")
def read_root():
    return {"message": "Welcome to the To-Do List API!"}
```

---

### ▶️ Run the API

To run the server:

```bash
uvicorn app.main:app --reload
```

Then visit [http://127.0.0.1:8000](http://127.0.0.1:8000) — you'll see:

```json
{"message": "Welcome to the To-Do List API!"}
```

Also try the built-in docs:  
[http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)


---

## Step 3: Set Up Database Connection – `database.py`

We’ll use **SQLAlchemy** and a **SQLite** database for simplicity (you can easily switch to PostgreSQL later).

---

### 📁 Create `app/database.py`

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# Use database URL from .env
SQLALCHEMY_DATABASE_URL = settings.DATABASE_URL

# SQLite requires this extra argument
connect_args = {"check_same_thread": False} if SQLALCHEMY_DATABASE_URL.startswith("sqlite") else {}

engine = create_engine(
    SQLALCHEMY_DATABASE_URL,
    connect_args=connect_args
)

SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)

Base = declarative_base()
```

---

### 🔁 Add DB Dependency for Routes – `dependencies.py`

📁 `app/dependencies.py`:

```python
from app.database import SessionLocal
from sqlalchemy.orm import Session
from fastapi import Depends

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```


---

## Step 4: Create Models – `models.py`

This file defines SQLAlchemy models for the `User` and `Todo` tables.

📁 `app/models.py`:

```python
from sqlalchemy import Column, Integer, String, ForeignKey, DateTime, Boolean
from sqlalchemy.orm import relationship
from app.database import Base
from datetime import datetime, timedelta

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String(50), unique=True, nullable=False)
    email = Column(String(100), unique=True, nullable=False)
    hashed_password = Column(String(100), nullable=False)

    todos = relationship("Todo", back_populates="owner")

class Todo(Base):
    __tablename__ = "todos"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, nullable=False)
    description = Column(String, nullable=True)
    status = Column(String, default="not_done")
    owner_id = Column(Integer, ForeignKey("users.id"))

    owner = relationship("User", back_populates="todos")

class RefreshToken(Base):
    __tablename__ = "refresh_tokens"

    id = Column(Integer, primary_key=True, index=True)
    token = Column(String, unique=True, nullable=False)
    user_id = Column(Integer, ForeignKey("users.id"))
    expires_at = Column(DateTime, nullable=False)
    is_revoked = Column(Boolean, default=False)

    user = relationship("User", backref="refresh_tokens")
```

---

### 🔎 Summary

We now have:
- `main.py` (entry point)
- `database.py` (DB connection)
- `models.py` (data models)
- `dependencies.py` (DB injection)

---

## Step 5: Create Pydantic Schemas – `schemas.py`

These schemas define what data is expected in requests and what should be returned in responses.

📁 `app/schemas.py`:

```python
from pydantic import BaseModel, Field, EmailStr
from typing import Optional, List

# ----------- USER SCHEMAS -----------

class UserBase(BaseModel):

    username: str = Field(..., example="johndoe")
    email: EmailStr = Field(..., example="johndoe@example.com")

class UserCreate(UserBase):
    password: str = Field(..., min_length=6, example="strongpassword123")

class UserLogin(BaseModel):
    email: EmailStr = Field(..., example="user@example.com")
    password: str = Field(..., example="strongpassword123")

class UserOut(UserBase):  # Inherits username and email
    id: int

    class Config:
        orm_mode = True


# ----------- TODO SCHEMAS -----------

class TodoBase(BaseModel):
    title: str = Field(..., example="Buy groceries")
    description: Optional[str] = Field(None, example="Milk, Bread, Eggs")
    status: Optional[str] = Field("not_done", example="done")

class TodoCreate(TodoBase):
    pass

class TodoUpdate(BaseModel):
    title: Optional[str]
    description: Optional[str]
    status: Optional[str]

class TodoOut(TodoBase):
    id: int
    owner_id: int

    class Config:
        orm_mode = True


# ----------- TOKEN SCHEMAS -----------

class Token(BaseModel):
    access_token: str
    refresh_token: str
    token_type: str

class TokenData(BaseModel):
    username: Optional[str] = None

class RefreshTokenRequest(BaseModel):
    refresh_token: str
```

---

### 🔍 Summary of Schemas

- `UserCreate` – used for registration
- `UserLogin` – used for login
- `UserOut` – used when returning user data
- `TodoCreate` – for creating todos
- `TodoUpdate` – for partial updates
- `TodoOut` – for API response
- `Token` / `TokenData` – for JWT handling


---

## Step 6: Authentication Logic – `auth.py`

This includes:
- Password hashing & verification with `bcrypt`
- JWT token creation and decoding with `python-jose`

---

### 📁 `app/auth.py`

```python
from datetime import datetime, timedelta
from typing import Optional
from jose import JWTError, jwt
from passlib.context import CryptContext
from fastapi import HTTPException, status, Depends
from fastapi.security import OAuth2PasswordBearer
from app import schemas, 
from app.config import settings
from app.models import User
from app.database import SessionLocal
from sqlalchemy.orm import Session
from app.dependencies import get_db
import uuid

# SECRET and Algorithm for JWT
SECRET_KEY = settings.SECRET_KEY
ALGORITHM = settings.ALGORITHM
ACCESS_TOKEN_EXPIRE_MINUTES = settings.ACCESS_TOKEN_EXPIRE_MINUTES

REFRESH_SECRET_KEY = settings.REFRESH_SECRET_KEY
REFRESH_TOKEN_EXPIRE_DAYS = settings.REFRESH_TOKEN_EXPIRE_DAYS

# Password hashing context
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

# OAuth2 scheme (used in protected routes)
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="/login")


# -------- Password Hashing -------- #

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    return pwd_context.verify(plain_password, hashed_password)


# -------- JWT Token Handling -------- #

def create_access_token(data: dict, expires_delta: Optional[timedelta] = None):
    to_encode = data.copy()

    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)

    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

def decode_access_token(token: str):
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception()
        return schemas.TokenData(username=username)
    except JWTError:
        raise credentials_exception()

def credentials_exception():
    return HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )


# -------- Get Current User -------- #

def get_current_user(
    token: str = Depends(oauth2_scheme),
    db: Session = Depends(get_db)
) -> User:
    token_data = decode_access_token(token)
    user = db.query(User).filter(User.username == token_data.username).first()
    if not user:
        raise credentials_exception()
    return user

# -------- Refresh Token Creation -------- #

def create_refresh_token(data: dict):
    expire = datetime.utcnow() + timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS)
    to_encode = data.copy()
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, REFRESH_SECRET_KEY, algorithm=ALGORITHM)

def decode_refresh_token(token: str):
    try:
        payload = jwt.decode(token, REFRESH_SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception()
        return schemas.TokenData(username=username)
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid refresh token")

def create_refresh_token_db(user: models.User, db: Session):
    raw_token = str(uuid.uuid4())
    encoded_token = jwt.encode({"sub": user.username}, REFRESH_SECRET_KEY, algorithm=ALGORITHM)

    expires_at = datetime.utcnow() + timedelta(days=REFRESH_TOKEN_EXPIRE_DAYS)
    refresh_token = models.RefreshToken(
        token=encoded_token,
        user_id=user.id,
        expires_at=expires_at,
        is_revoked=False
    )

    db.add(refresh_token)
    db.commit()
    return encoded_token

def verify_refresh_token_db(token: str, db: Session):
    try:
        payload = jwt.decode(token, REFRESH_SECRET_KEY, algorithms=[ALGORITHM])
        username = payload.get("sub")
    except JWTError:
        raise HTTPException(status_code=401, detail="Invalid refresh token")

    token_entry = db.query(models.RefreshToken).filter(models.RefreshToken.token == token).first()

    if not token_entry or token_entry.is_revoked or token_entry.expires_at < datetime.utcnow():
        raise HTTPException(status_code=401, detail="Refresh token expired or revoked")

    return username
```

---

### 🔐 Summary

- `hash_password()` & `verify_password()` handle secure password storage
- `create_access_token()` generates JWT tokens
- `decode_access_token()` validates and extracts user data
- `get_current_user()` is a FastAPI dependency for protected routes


---

## Step 7: User Routes – `routes/user.py`

This includes:
- **`POST /register`** – Create new user
- **`POST /login`** – Authenticate and return JWT

---

### 📄 `app/routes/user.py`

```python
from fastapi import APIRouter, Depends, HTTPException, status
from sqlalchemy.orm import Session
from fastapi.security import OAuth2PasswordRequestForm

from app import schemas, models, auth
from app.dependencies import get_db

router = APIRouter(tags=["Users"])


# -------- Register -------- #
@router.post("/register", response_model=schemas.UserOut)
def register(user: schemas.UserCreate, db: Session = Depends(get_db)):
    existing_username = db.query(models.User).filter(models.User.username == user.username).first()
    existing_email = db.query(models.User).filter(models.User.email == user.email).first()

    if existing_username:
        raise HTTPException(status_code=400, detail="Username already exists")
    if existing_email:
        raise HTTPException(status_code=400, detail="Email already exists")

    hashed_password = auth.hash_password(user.password)
    new_user = models.User(
        username=user.username,
        email=user.email,
        hashed_password=hashed_password,
    )

    db.add(new_user)
    db.commit()
    db.refresh(new_user)

    return new_user

# -------- Login -------- #
@router.post("/login", response_model=schemas.Token)
def login(user_login: schemas.UserLogin, db: Session = Depends(get_db)):
    user = db.query(models.User).filter(models.User.email == user_login.email).first()

    if not user or not auth.verify_password(user_login.password, user.hashed_password):
        raise HTTPException(status_code=401, detail="Invalid credentials")

    access_token = auth.create_access_token(data={"sub": user.username})
    refresh_token = auth.create_refresh_token_db(user=user, db=db)

    return {
        "access_token": access_token,
        "refresh_token": refresh_token,
        "token_type": "bearer"
    }

# --------- Refresh Token ----------#
@router.post("/refresh-token", response_model=schemas.Token)
def refresh_token(req: schemas.RefreshTokenRequest, db: Session = Depends(get_db)):
    username = auth.verify_refresh_token_db(req.refresh_token, db)
    user = db.query(models.User).filter_by(username=username).first()

    if not user:
        raise HTTPException(status_code=401, detail="User not found")

    access_token = auth.create_access_token(data={"sub": username})
    new_refresh_token = auth.create_refresh_token_db(user=user, db=db)

    return {
        "access_token": access_token,
        "refresh_token": new_refresh_token,
        "token_type": "bearer"
    }

# --------- Logout ----------#
@router.post("/logout")
def logout(req: schemas.RefreshTokenRequest, db: Session = Depends(get_db)):
    token_entry = db.query(models.RefreshToken).filter_by(token=req.refresh_token).first()

    if not token_entry or token_entry.is_revoked:
        raise HTTPException(status_code=401, detail="Token already invalid or not found")

    token_entry.is_revoked = True
    db.commit()

    return {"detail": "Logged out successfully"}
```

---

### ✅ Update `main.py` to Include Routes

📄 `app/main.py` (add imports & route registration):

```python
from app.database import Base, engine
from app.routes import user  # 👈 Import user routes

# Create all tables
Base.metadata.create_all(bind=engine)

# Include routers
app.include_router(user.router)
```

---
## Step 8: To-do Routes – `routes/todo.py`

This includes:
- `POST /todos/` – Create new to-do item
- `GET /todos/` – List todos (with pagination, filters)
- `GET /todos/{id}` – Get specific todo
- `PUT /todos/{id}` – Update todo
- `DELETE /todos/{id}` – Delete todo

All endpoints are **protected**: users must be authenticated via JWT.

---

### 📁 `app/routes/todo.py`

```python
from fastapi import APIRouter, Depends, HTTPException, Query
from sqlalchemy.orm import Session
from typing import List, Optional

from app import models, schemas, auth
from app.dependencies import get_db

router = APIRouter(tags=["Todos"])


# -------- CREATE -------- #
@router.post("/todos/", response_model=schemas.TodoOut)
def create_todo(
    todo: schemas.TodoCreate,
    db: Session = Depends(get_db),
    current_user: models.User = Depends(auth.get_current_user)
):
    new_todo = models.Todo(**todo.dict(), owner_id=current_user.id)
    db.add(new_todo)
    db.commit()
    db.refresh(new_todo)
    return new_todo


# -------- LIST -------- #
@router.get("/todos/", response_model=List[schemas.TodoOut])
def get_todos(
    status: Optional[str] = Query(None),
    sort: Optional[str] = Query("id"),
    limit: int = Query(10, ge=1),
    offset: int = Query(0, ge=0),
    db: Session = Depends(get_db),
    current_user: models.User = Depends(auth.get_current_user)
):
    query = db.query(models.Todo).filter(models.Todo.owner_id == current_user.id)

    if status:
        query = query.filter(models.Todo.status == status)

    if sort in ["id", "title", "status"]:
        query = query.order_by(getattr(models.Todo, sort))

    return query.offset(offset).limit(limit).all()


# -------- GET ONE -------- #
@router.get("/todos/{todo_id}", response_model=schemas.TodoOut)
def get_todo(
    todo_id: int,
    db: Session = Depends(get_db),
    current_user: models.User = Depends(auth.get_current_user)
):
    todo = db.query(models.Todo).filter_by(id=todo_id, owner_id=current_user.id).first()
    if not todo:
        raise HTTPException(status_code=404, detail="Todo not found")
    return todo


# -------- UPDATE -------- #
@router.put("/todos/{todo_id}", response_model=schemas.TodoOut)
def update_todo(
    todo_id: int,
    updated_data: schemas.TodoUpdate,
    db: Session = Depends(get_db),
    current_user: models.User = Depends(auth.get_current_user)
):
    todo = db.query(models.Todo).filter_by(id=todo_id, owner_id=current_user.id).first()
    if not todo:
        raise HTTPException(status_code=404, detail="Todo not found")

    for key, value in updated_data.dict(exclude_unset=True).items():
        setattr(todo, key, value)

    db.commit()
    db.refresh(todo)
    return todo


# -------- DELETE -------- #
@router.delete("/todos/{todo_id}")
def delete_todo(
    todo_id: int,
    db: Session = Depends(get_db),
    current_user: models.User = Depends(auth.get_current_user)
):
    todo = db.query(models.Todo).filter_by(id=todo_id, owner_id=current_user.id).first()
    if not todo:
        raise HTTPException(status_code=404, detail="Todo not found")

    db.delete(todo)
    db.commit()
    return {"detail": "Todo deleted"}
```

---

### ✅ Update `main.py` to Include To-do Routes

📄 `app/main.py`:

```python
from app.routes import user, todo  # Add `todo`

# Include routers
app.include_router(user.router)
app.include_router(todo.router)
```

---

### ✅ Test Authenticated Endpoints

Use the `/login` endpoint to get a token, then test:

- `POST /todos/`
- `GET /todos/?status=done&limit=5&offset=0`
- `PUT /todos/{id}`
- `DELETE /todos/{id}`

All should work with the JWT **Authorization** header:

```http
Authorization: Bearer your_token_here
```

----


## Step 9: Postman Test

Here's a complete, professional **Postman testing guide** for your FastAPI To-Do List API with:

✅ User registration  
✅ Email-based login  
✅ JWT + Refresh tokens  
✅ Logout  
✅ CRUD for to-dos  
✅ Pagination, filtering, and protected routes

---

### 🧪 Postman Collection Setup

### 🔧 Base URL (if using Uvicorn locally):

```http
http://127.0.0.1:8000
```

---

### ✅ 1. Register New User

**POST** `/register`

- **URL:** `POST /register`
- **Headers:** `Content-Type: application/json`
- **Body (raw JSON):**

```json
{
  "email": "testuser@example.com",
  "username": "testuser",
  "password": "password123"
}
```

✅ **Expected:** 201 Created  
✅ **Returns:** `{ "id": 1, "email": "...", "username": "..." }`

---

### ✅ 2. Login to Get Access and Refresh Tokens

**POST** `/login`
- **Headers:** `Content-Type: application/json`
- **Body:**

```json
{
  "email": "testuser@example.com",
  "password": "password123"
}
```

✅ **Returns:**

```json
{
  "access_token": "jwt...",
  "refresh_token": "refresh.jwt...",
  "token_type": "bearer"
}
```

📌 Save the `access_token` and `refresh_token` for use in next steps.

---

### ✅ 3. Create a To-Do Item (Authenticated)

**POST** `/todos/`

- **Headers:**
    - `Authorization: Bearer <access_token>`
    - `Content-Type: application/json`
- **Body:**

```json
{
  "title": "Write Postman Guide",
  "description": "Document all endpoints and testing steps",
  "status": "not_done"
}
```

✅ **Returns:** To-do data with `id`, `title`, `owner_id`, etc.

---

### ✅ 4. Get All To-Dos (Authenticated, Paginated)

**GET** `/todos/?skip=0&limit=10`

- **Headers:**
    - `Authorization: Bearer <access_token>`

✅ **Returns:** A list of your todos  
✅ Supports pagination with `skip`, `limit`

---

### ✅ 5. Filter To-Dos by Status

**GET** `/todos/?status=done`  
✅ Returns only done items

---

### ✅ 6. Update a To-Do Item

**PUT** 

```
/todos/{todo_id}
```

- **URL:** `/todos/1`
- **Headers:** `Authorization: Bearer <access_token>`
- **Body:**

```json
{
  "title": "Write API Test Guide",
  "status": "done"
}
```

✅ Updates the title and status

---

### ✅ 7. Delete a To-Do

**DELETE**

```
/todos/{todo_id}
```
- **URL:** `/todos/1`
- **Headers:** `Authorization: Bearer <access_token>`

✅ Returns 204 No Content

---

### ✅ 8. Refresh Access Token

**POST** `/token/refresh`
- **Body:**

```json
{
  "refresh_token": "<your_refresh_token>"
}
```

✅ **Returns new `access_token`**

> Save and use this new `access_token` for future requests.

---

### ✅ 9. Logout

**POST** `/logout`
- **Headers:** `Authorization: Bearer <access_token>`

✅ Invalidates the refresh token stored in the DB (if implemented)  
✅ You’ll need to log in again after logout

---

### ✅ 10. Get Current User Profile

**GET** `/users/me`
- **Headers:** `Authorization: Bearer <access_token>`

✅ Returns the currently authenticated user

---

### 🧪 Recommended Postman Organization

Create a collection like this:

```
📁 To-Do API Test Collection
├── 🔐 Register
├── 🔐 Login
├── 🔁 Refresh Token
├── ❌ Logout
├── 📄 Get Profile
├── ✅ Create Todo
├── 📃 Get Todos
├── 🔍 Filter Todos
├── ✏️ Update Todo
└── ❌ Delete Todo
```

You can **set access_token and refresh_token** as Postman environment variables to use in all requests automatically.

---

### 🛠 Optional: Add Pre-Request Scripts

To auto-refresh tokens or set Authorization headers dynamically, use:

**Pre-request Script:**

```js
pm.request.headers.add({
  key: 'Authorization',
  value: 'Bearer ' + pm.environment.get("access_token")
});
```

---

