 We’ll test the `POST /upload/` endpoint to upload a Markdown file, and the `GET /notes/` and `GET /notes/{id}` endpoints to retrieve notes.

---

### 🧱 Prerequisites

1. ✅ Your FastAPI server is running:

```bash
uvicorn app.main:app --reload
```

It should say:

```
Uvicorn running on http://127.0.0.1:8000
```

2. ✅ You have Postman installed. If not, download it from [https://www.postman.com/downloads/](https://www.postman.com/downloads/)

---

## 🟡 1. Test: Upload a Markdown File (POST /upload/)

### 🔧 Setup in Postman

1. **Open Postman** and click **"New" → "HTTP Request"**

2. Select:

   * Method: `POST`
   * URL: `http://127.0.0.1:8000/upload/`

3. Go to the **Body** tab → Select **form-data**

4. Add the following fields:

| Key   | Type | Value                  |
| ----- | ---- | ---------------------- |
| title | Text | My First Note          |
| file  | File | Select your `.md` file |

> Click **“Choose Files”** under file to pick your markdown file.

### 🔄 Send the Request

Click the **blue "Send"** button.

### ✅ Expected Response

You’ll get back a JSON object like this:

```json
{
  "id": 1,
  "title": "My First Note",
  "content": "# My Note\nThis is a markdown note.",
  "html_content": "<h1>My Note</h1><p>This is a markdown note.</p>",
  "grammar_issues": "SOME_GRAMMAR_CHECK_OUTPUT"
}
```

---

## 🟢 2. Test: Get All Notes (GET /notes/)

### 🔧 Setup in Postman

1. Create a new request
2. Method: `GET`
3. URL: `http://127.0.0.1:8000/notes/`

> No need to add any headers or body.

### 🔄 Send the Request

Click **Send**

### ✅ Expected Response

A list of notes:

```json
[
  {
    "id": 1,
    "title": "My First Note",
    "content": "...",
    "html_content": "...",
    "grammar_issues": "..."
  }
]
```

---

## 🔵 3. Test: Get Single Note by ID (GET /notes/{id})

### 🔧 Setup in Postman

1. Create a new request
2. Method: `GET`
3. URL: `http://127.0.0.1:8000/notes/1` (replace `1` with your note’s ID)

### 🔄 Send the Request

Click **Send**

### ✅ Expected Response

You’ll get back the specific note as JSON.

---

## 📌 Helpful Tips in Postman

* Use tabs to test different endpoints.
* Save your requests in a collection.
* Use `Pretty` tab in Postman to nicely format JSON.
* Use the **Console** (bottom left in Postman) to debug.

---

## 🚨 Common Errors

| Error                               | Reason                                                 |
| ----------------------------------- | ------------------------------------------------------ |
| 422 Unprocessable Entity            | Missing title or file in the form-data                 |
| 400 Only markdown files are allowed | You uploaded something other than `.md`                |
| 404 Note not found                  | The `id` you requested doesn’t exist in the database   |
| Connection error                    | Your FastAPI server is not running on `localhost:8000` |

---

