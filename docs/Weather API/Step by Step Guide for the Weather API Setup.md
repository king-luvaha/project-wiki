Here's a **step-by-step guide** for setting up your **Weather API** on a **Windows machine**, using **Flask**, **Redis (Cloud)**, and **Visual Crossing Weather API**.

---

### 🧰 Requirements

Before starting, ensure you have the following:

- ✅ Python 3.x installed on Windows
- ✅ A code editor like VS Code
- ✅ Redis Cloud account (e.g., [Redis Cloud by Redis Enterprise](https://redis.com/redis-enterprise/cloud/))
- ✅ Visual Crossing API Key ([Sign up here](https://www.visualcrossing.com/weather-api))

---

### 🧱 Step 1: Create Your Project Directory

Open your terminal (Command Prompt, PowerShell, or VS Code Terminal) and run:

```bash
mkdir weather-api
cd weather-api
```

---

### 📄 Step 2: Create Project Files

Create the following files manually or using this command (on Git Bash or WSL):

```bash
touch app.py weather_api.py cache.py .env requirements.txt
```

Otherwise, just **create them manually** inside the folder.

Project Structure:

```bash

weather-api/
│
├── app.py                # Main Flask app
├── cache.py              # Redis cache logic
├── weather.py            # Weather API logic (calls Visual Crossing)
├── .env                  # Environment variables
├── requirements.txt      # Project dependencies
└── README.md

```

---

### 📦 Step 3: Create and Activate Virtual Environment (Recommended)

```bash
python -m venv venv
venv\Scripts\activate
```

You should now see `(venv)` in your terminal prompt.

---

### 📥 Step 4: Install Required Python Packages

Run:

```bash
pip install flask requests python-dotenv redis flask-limiter
```

Save dependencies:

```bash
pip freeze > requirements.txt
```

---

### 🧪 Step 5: Add Your `.env` File

Create a `.env` file in the root of your project with:

```env
API_KEY=your_visual_crossing_api_key
REDIS_URL=your_redis_cloud_url
```

- **API_KEY**: Get from your [Visual Crossing account](https://www.visualcrossing.com/weather-api)
- **REDIS_URL**: Get from your Redis cloud account; it looks like this:

```
redis://default:<password>@<host>:<port>
```

Example:

```
redis://default:Ac6oAAIjcD@redis-12345.c99.us-east-1-4.ec2.cloud.redislabs.com:12345
```

---

### 🧠 Step 6: Add the Code

#### `app.py`

```python
from flask import Flask, request, jsonify
from dotenv import load_dotenv
import os
from weather_api import get_weather
from cache import cache_get, cache_set
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

load_dotenv()

app = Flask(__name__)
limiter = Limiter(app=app, key_func=get_remote_address)

@app.route('/')
def home():
    return {"message": "Welcome to the Weather API!"}

@app.route('/weather')
@limiter.limit("10/minute")
def weather():
    city = request.args.get('city')
    if not city:
        return jsonify({"error": "City parameter is required"}), 400

    cached = cache_get(city)
    if cached:
        return jsonify({"source": "cache", "data": cached})

    try:
        data = get_weather(city)
        cache_set(city, data, ex=43200)
        return jsonify({"source": "api", "data": data})
    except Exception as e:
        return jsonify({"error": str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True)
```

---

#### `weather_api.py`

```python
import requests
import os

API_KEY = os.getenv("API_KEY")

def get_weather(city):
    url = f"https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/{city}?unitGroup=metric&key={API_KEY}&contentType=json"
    response = requests.get(url)

    if response.status_code != 200:
        raise Exception("Error fetching weather data")

    return response.json()
```

---

#### `cache.py`

```python
import redis
import os
import json

redis_url = os.getenv("REDIS_URL", "redis://localhost:6379")
r = redis.Redis.from_url(redis_url)

def cache_get(key):
    data = r.get(key)
    if data:
        return json.loads(data)
    return None

def cache_set(key, value, ex=43200):
    r.set(key, json.dumps(value), ex=ex)
```

---

#### `requirements.txt`

```txt
flask
requests
python-dotenv
redis
flask-limiter
```

---

### 🧪 Step 7: Run Your Flask Server

In your terminal, make sure your virtual environment is activated, then:

```bash
python app.py
```

---

### 🌐 Step 8: Test the API

Open your browser or Postman:

```
http://127.0.0.1:5000/weather?city=Nairobi
```

✅ First response: fetched from API  
✅ Second response (within 12 hrs): fetched from **Redis cache**

---

### 🔐 Notes on Redis Cloud:

- If Redis uses **TLS/SSL**, make sure your Redis host ends with `rediss://` not `redis://`.
    
    - In that case, update your `.env` to:
        
        ```
        REDIS_URL=rediss://default:<password>@<host>:<port>
        ```
        
- `redis-py` automatically handles SSL for `rediss://` URLs.

---

### ✅ Final Tips

- ✔ Use `.gitignore` to exclude `venv` and `.env`
- ✔ You can deploy this to Render, Railway, or Heroku (free platforms for testing APIs)
- ✔ Add Swagger UI or Postman collection to document your API

---
