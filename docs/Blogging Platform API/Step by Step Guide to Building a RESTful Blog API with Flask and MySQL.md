
Welcome to this beginner-friendly guide on building a simple blog API using Python's Flask framework and MySQL as the backend database. By the end of this tutorial, you’ll have a fully functional API that supports creating, reading, updating, deleting, and searching blog posts.

---

## ✨ What You'll Build

A RESTful API with endpoints to:

- Create a blog post
- Read single or all posts
- Update a post
- Delete a post
- Search posts by keyword (title, content, category)

---

## 🚀 Tech Stack

- **Backend**: Flask (Python)
- **Database**: MySQL (via MySQL Workbench)
- **ORM**: Raw SQL with `flask-mysqldb`
- **Extras**: `python-dotenv` for environment variables, `flask-cors` for cross-origin access

---

## 📁 Folder Structure

```

blog-api/  
├── app.py             # Main Flask application  
├── .env               # Environment variables (DO NOT COMMIT)  
├── .gitignore         # Git ignore list  
├── requirements.txt   # Python dependencies  
└── README.md          # Setup and usage guide
```


---

## 📆 Step 1: Project Setup

### ✅ Prerequisites

- Python 3.7+
- MySQL Server (preferably via MySQL Workbench)
- Git

### 🔧 Set Up Your Folder

```bash
mkdir blog-api
cd blog-api
python -m venv venv
venv/Scripts/activate  # Windows
```

### 🔹 Install Required Packages

```bash
pip install flask flask-mysqldb flask-cors python-dotenv
pip freeze > requirements.txt
```

### 📄 Create Starter Files

- `app.py` (main backend logic)
- `.env` (credentials)
- `.gitignore`

---

## 🔐 Step 2: Environment Variables

Create a `.env` file:

```env
MYSQL_HOST=localhost
MYSQL_USER=root
MYSQL_PASSWORD=yourpassword
MYSQL_DB=blogdb
MYSQL_PORT=3306          # Default port is '3306'. You can change to preffered PORT
```

Why? To keep your MySQL credentials secure and separate from code.

---

## 📊 Step 3: Set Up the MySQL Database

Open **MySQL Workbench** and run this SQL:

```sql
CREATE DATABASE IF NOT EXISTS blogdb;
USE blogdb;

CREATE TABLE IF NOT EXISTS posts (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255),
    content TEXT,
    category VARCHAR(100),
    tags TEXT,
    created_at DATETIME,
    updated_at DATETIME
);
```

---

## 📂 Step 4: Initialize Flask App

In `app.py`, write:

```python
import os
import json
from datetime import datetime
from flask import Flask, request, jsonify
from flask_mysqldb import MySQL
from flask_cors import CORS
from dotenv import load_dotenv

load_dotenv()

app = Flask(__name__)
CORS(app)

# Load config from .env
app.config['MYSQL_HOST'] = os.getenv('MYSQL_HOST')
app.config['MYSQL_USER'] = os.getenv('MYSQL_USER')
app.config['MYSQL_PASSWORD'] = os.getenv('MYSQL_PASSWORD')
app.config['MYSQL_DB'] = os.getenv('MYSQL_DB')
app.config['MYSQL_PORT'] = int(os.getenv('MYSQL_PORT', 3306))

mysql = MySQL(app)

# Helper function to serialize rows
def serialize_post(row):
    return {
        "id": row[0],
        "title": row[1],
        "content": row[2],
        "category": row[3],
        "tags": json.loads(row[4]),
        "createdAt": row[5].isoformat(),
        "updatedAt": row[6].isoformat()
    }
```

---

##  🔚 Step 5: Implement CRUD Endpoints

### ➕ Create a Post

```python
@app.route('/posts', methods=['POST'])
def create_post():
    data = request.get_json()
    if not all(field in data for field in ['title', 'content', 'category', 'tags']):
        return jsonify({"error": "Missing fields"}), 400

    now = datetime.utcnow()
    cursor = mysql.connection.cursor()
    cursor.execute("""
        INSERT INTO posts (title, content, category, tags, created_at, updated_at)
        VALUES (%s, %s, %s, %s, %s, %s)
    """, (data['title'], data['content'], data['category'], json.dumps(data['tags']), now, now))
    mysql.connection.commit()
    post_id = cursor.lastrowid
    cursor.close()

    return jsonify({
        "id": post_id,
        **data,
        "createdAt": now.isoformat(),
        "updatedAt": now.isoformat()
    }), 201
```

### 🔀 Update a Post

```python
@app.route('/posts/<int:post_id>', methods=['PUT'])
def update_post(post_id):
    data = request.get_json()
    if not all(field in data for field in ['title', 'content', 'category', 'tags']):
        return jsonify({"error": "Missing fields"}), 400

    cursor = mysql.connection.cursor()
    cursor.execute("SELECT * FROM posts WHERE id = %s", (post_id,))
    row = cursor.fetchone()
    if not row:
        return jsonify({"error": "Post not found"}), 404

    now = datetime.utcnow()
    cursor.execute("""
        UPDATE posts SET title = %s, content = %s, category = %s, tags = %s, updated_at = %s
        WHERE id = %s
    """, (data['title'], data['content'], data['category'], json.dumps(data['tags']), now, post_id))
    mysql.connection.commit()
    cursor.close()

    return jsonify({
        "id": post_id,
        **data,
        "createdAt": row[5].isoformat(),
        "updatedAt": now.isoformat()
    })
```

### 🚮 Delete a Post

```python
@app.route('/posts/<int:post_id>', methods=['DELETE'])
def delete_post(post_id):
    cursor = mysql.connection.cursor()
    cursor.execute("SELECT id FROM posts WHERE id = %s", (post_id,))
    if not cursor.fetchone():
        return jsonify({"error": "Post not found"}), 404

    cursor.execute("DELETE FROM posts WHERE id = %s", (post_id,))
    mysql.connection.commit()
    cursor.close()
    return '', 204
```

### 📄 Get One Post

```python
@app.route('/posts/<int:post_id>', methods=['GET'])
def get_post(post_id):
    cursor = mysql.connection.cursor()
    cursor.execute("SELECT * FROM posts WHERE id = %s", (post_id,))
    row = cursor.fetchone()
    cursor.close()
    if not row:
        return jsonify({"error": "Post not found"}), 404
    return jsonify(serialize_post(row))
```

### 📃 Get All / Search Posts

```python
@app.route('/posts', methods=['GET'])
def get_all_posts():
    term = request.args.get('term', '')
    cursor = mysql.connection.cursor()
    if term:
        query = """
            SELECT * FROM posts
            WHERE title LIKE %s OR content LIKE %s OR category LIKE %s
        """
        wildcard = f"%{term}%"
        cursor.execute(query, (wildcard, wildcard, wildcard))
    else:
        cursor.execute("SELECT * FROM posts")
    rows = cursor.fetchall()
    cursor.close()
    return jsonify([serialize_post(row) for row in rows])
```

---

## 🔍 Step 6: Test the API

Use tools like Postman or Thunder Client:

- `POST /posts` to add
- `GET /posts` to view
- `PUT /posts/1` to update
- `DELETE /posts/1` to delete
- `GET /posts?term=python` to search

Example JSON:

```json
{
  "title": "Hello World",
  "content": "My first blog post!",
  "category": "Intro",
  "tags": ["Flask", "MySQL"]
}
```

---

## 🏁 Step 7: Run the Application

At the bottom of your `app.py` file, include:

```
if __name__ == '__main__':
    app.run(debug=True)
```

### ✅ Why this is important

This block ensures that the Flask development server starts **only** if the script is executed directly (e.g., via `python app.py`). This is a Python best practice that avoids running code unintentionally when the module is imported elsewhere.

- `debug=True` enables hot reloading and detailed error messages.

---

## 🚀 What’s Next?

- Add pagination
- Add user accounts and auth
- Deploy to a platform like Render
- Connect it to a frontend (e.g., React)

---

## 🎨 Bonus: Frontend Integration with React (Educational Only)

If you'd like to build a simple frontend to consume this API, here’s a very basic idea using React:

### 🛠️ Setup

```
npx create-react-app blog-client
cd blog-client
npm install axios
```

### 📄 Fetch All Posts Example

In `App.js`:

```javascript
import { useEffect, useState } from 'react';
import axios from 'axios';

function App() {
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    axios.get('http://localhost:5000/posts')
      .then(res => setPosts(res.data))
      .catch(err => console.error(err));
  }, []);

  return (
    <div>
      <h1>Blog Posts</h1>
      {posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.content}</p>
        </div>
      ))}
    </div>
  );
}

export default App;
```

Make sure CORS is enabled in Flask and both backend (`localhost:5000`) and frontend (`localhost:3000`) are running.


---

## 😊 Conclusion

You now have a working blog API using Flask and MySQL. This is a solid foundation for real-world projects or learning backend development.

Happy building! 🚀