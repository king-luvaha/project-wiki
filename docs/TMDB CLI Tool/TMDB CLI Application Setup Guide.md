
Fetch and display popular, top-rated, now-playing, or upcoming movies right in your terminal using The Movie Database (TMDB) API.

---

## ✅ Prerequisites

* Python 3.7+
* A terminal or command prompt
* Internet connection
* A TMDB account and API key (free)

---

## 🚀 Step-by-Step Setup

### **1. Create Your Project Folder**

```bash
mkdir tmdb-cli
cd tmdb-cli
```

---

### **2. Set Up a Virtual Environment (Recommended)**

```bash
python -m venv venv
```

Activate the virtual environment:

* **Windows**:

  ```bash
  venv\Scripts\activate
  ```

* **macOS/Linux**:

  ```bash
  source venv/bin/activate
  ```

---

### **3. Install Required Packages**

```bash
pip install requests python-dotenv colorama tabulate
```

---

### **4. Get Your TMDB API Key**

1. Sign up at [https://www.themoviedb.org/signup](https://www.themoviedb.org/signup)
2. Confirm your email and login
3. Go to [API settings](https://www.themoviedb.org/settings/api)
4. Click **Create** under API Key (v3 auth)
5. Copy your **API Key (v3 auth)**

---

### **5. Create the `.env` File**

In your project directory, create a `.env` file and paste your API key:

```bash
TMDB_API_KEY=your_actual_api_key_here
```

---

### **6. Create the Python Script**

Create a file named `tmdb_app.py` and paste the polished code below:

```python
import requests
import argparse
import os
from dotenv import load_dotenv
from colorama import init, Fore
from tabulate import tabulate

init(autoreset=True) # Initialize coloroma

# Load APIkey
load_dotenv()
API_KEY = os.getenv("TMDB_API_KEY") or "PASTE_YOUR_API_KEY_HERE"

BASE_URL = "https://api.themoviedb.org/3/movie"

MOVIE_TYPES = {
    "playing": "now_playing",
    "popular": "popular",
    "top": "top_rated",
    "upcoming": "upcoming"
}

# Core Function
def fetch_movies(movie_type: str):
    if movie_type not in MOVIE_TYPES:
        print(Fore.RED + f"❌ Invalid type: '{movie_type}'. Must be one of {list(MOVIE_TYPES.keys())}")
        return

    url = f"{BASE_URL}/{MOVIE_TYPES[movie_type]}"
    params = {
        "api_key": API_KEY,
        "language": "en-US",
        "page": 1
    }

    response = requests.get(url, params=params)

    if response.status_code != 200:
        print(Fore.RED + f"❌ Failed to fetch data: {response.status_code}")
        return

	data = response.json()
    results = data.get("results", [])

    if not results:
        print(Fore.YELLOW + "⚠️ No movies found.")
        return

    print(f"\n🎬 {Fore.CYAN + movie_type.upper()} MOVIES\n" + "=" * 60)

    table_data = []
    for i, movie in enumerate(results[:10], 1):
        title = movie.get("title", "N/A")
        rating = movie.get("vote_average", "N/A")
        release = movie.get("release_date", "N/A")
        table_data.append([f"{i}", title, release, f"{rating} ⭐"])

    headers = [Fore.YELLOW + "#", Fore.GREEN + "Title", Fore.MAGENTA + "Release Date", Fore.BLUE + "Rating"]
    print(tabulate(table, headers=headers, tablefmt="fancy_grid"))

# CLI Argument Parsing
def main():
    parser = argparse.ArgumentParser(description="🎥 TMDB CLI Movie Fetcher")
    parser.add_argument("--type", required=True, help="Choose: playing, popular, top, upcoming")
    args = parser.parse_args()
    fetch_movies(args.type.lower())

# Entry Point
if __name__ == "__main__":
    main()
```

---

### **7. Run the Application**

Use this command:

```bash
python tmdb_app.py --type popular
```

Replace `popular` with:

* `playing`
* `top`
* `upcoming`

Example:

```bash
python tmdb_app.py --type top
```

---

## ✅ Optional Enhancements

### 🎯 Make It Executable with a Shortcut

Create a `tmdb-app.bat` file on Windows in the same folder:

```bat
@echo off
python tmdb_app.py %*
```

Then just run:

```bash
tmdb-app --type upcoming
```

Or on UNIX:

```bash
chmod +x tmdb_app.py
mv tmdb_app.py /usr/local/bin/tmdb-app
tmdb-app --type top
```

---

## 📁 Final Project Structure

```
tmdb-cli/
│
├── venv/                  # Virtual environment
├── .env                   # Your API key
├── tmdb_app.py            # Main script
└── requirements.txt       # (Optional) for pip install
```

To create `requirements.txt`:

```bash
pip freeze > requirements.txt
```

---

## ✅ You're Done!

You now have a functional, stylish terminal-based movie browser!
Would you like to add search-by-title or export-to-CSV next?
