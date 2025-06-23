Great! Here's a **step-by-step guide** you can use (or adapt) for your blog to teach your audience how to build the **GitHub Activity Fetcher CLI app in Python** from scratch. It's written in a way that walks beginners through each concept and builds their understanding as they go.

---

## 🚀 Introduction

In this tutorial, we’ll create a command-line interface (CLI) tool in Python that fetches and displays a user’s recent GitHub activity. This is a great mini project for practicing:

- Working with APIs
- Parsing JSON
- Handling HTTP errors
- Formatting data for terminal output

---

## 🧱 Step 1: Set Up Your Environment

### ✅ Requirements

Make sure you have:

- Python 3.x installed
- Access to a terminal or command prompt
- `requests` library installed

If you don’t have `requests`, install it via pip:

```bash
pip install requests
```

---

## 📁 Step 2: Create the Project File

Let’s start by creating a new Python file.

```bash
touch github_activity.py
```

Then open it in your code editor (e.g., VS Code).

---

## 🧠 Step 3: Import the Required Modules

At the top of your file, add the imports. Each module serves a purpose:

```python
import sys
import requests
import json
from datetime import datetime
```

- `sys`: to handle command-line arguments.
- `requests`: to make HTTP requests to GitHub.
- `json`: to work with the data returned from GitHub.
- `datetime`: to make timestamps user-friendly.

---

## 🌐 Step 4: Fetch GitHub User Activity

Now we’ll write a function to get a user’s activity using GitHub’s public API.

```python
def fetch_user_activity(username):
    url = f"https://api.github.com/users/{username}/events"
    try:
        response = requests.get(url)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.HTTPError as http_err:
        if response.status_code == 404:
            print(f"Error: User '{username}' not found on GitHub.")
        else:
            print(f"HTTP error occurred: {http_err}")
    except requests.exceptions.RequestException as err:
        print(f"Error fetching data: {err}")
    return None
```

> 🔍 Tip: We use `.raise_for_status()` to automatically throw an error if something goes wrong (like a 404).

---

## 🔎 Step 5: Parse the Activity Data

Let’s convert the raw JSON into readable messages based on event types.

```python
def parse_activity(events):
    if not events:
        return []

    activity_messages = []

    for event in events:
        event_type = event.get('type')
        repo_name = event.get('repo', {}).get('name', 'unknown repository')
        created_at = event.get('created_at', '')

        try:
            date_obj = datetime.strptime(created_at, "%Y-%m-%dT%H:%M:%SZ")
            formatted_date = date_obj.strftime("%b %d, %Y at %H:%M")
        except (ValueError, TypeError):
            formatted_date = "an unknown time"

        payload = event.get('payload', {})

        if event_type == "PushEvent":
            commits = payload.get('commits', [])
            commit_count = len(commits)
            message = f"Pushed {commit_count} commit{'s' if commit_count != 1 else ''} to {repo_name}"

        elif event_type == "IssuesEvent":
            action = payload.get('action', 'did something')
            title = payload.get('issue', {}).get('title', 'an issue')
            message = f"{action.capitalize()} issue '{title}' in {repo_name}"

        elif event_type == "PullRequestEvent":
            action = payload.get('action', 'did something')
            title = payload.get('pull_request', {}).get('title', 'a pull request')
            message = f"{action.capitalize()} pull request '{title}' in {repo_name}"

        elif event_type == "WatchEvent":
            message = f"Starred {repo_name}"

        elif event_type == "ForkEvent":
            fork_url = payload.get('forkee', {}).get('html_url', 'a repository')
            message = f"Forked {repo_name} to {fork_url}"

        elif event_type == "CreateEvent":
            ref_type = payload.get('ref_type', 'something')
            message = f"Created a {ref_type} in {repo_name}"

        else:
            message = f"Performed {event_type} on {repo_name}"

        activity_messages.append(f"- {message} (on {formatted_date})")

    return activity_messages
```

---

## 🖥 Step 6: Display the Activity to the Terminal

Add a function to show the results in a clean format.

```python
def display_activity(username, activities):
    if not activities:
        print(f"No recent activity found for {username} or the account is private.")
        return

    print(f"\nRecent activity for {username}:\n")
    for activity in activities[:10]:
        print(activity)
    print("\n")
```

---

## 🧪 Step 7: Add the `main()` Function

This part ties everything together and handles command-line input.

```python
def main():
    if len(sys.argv) != 2:
        print("Usage: python github_activity.py <username>")
        sys.exit(1)

    username = sys.argv[1]
    print(f"\nFetching GitHub activity for {username}...\n")

    raw_activity = fetch_user_activity(username)
    if raw_activity is None:
        sys.exit(1)

    parsed_activity = parse_activity(raw_activity)
    display_activity(username, parsed_activity)
```

---

## 🔁 Step 8: Run the Script

At the bottom of the file, add the standard Python entry point:

```python
if __name__ == "__main__":
    main()
```

Now you can run the script from your terminal like this:

```bash
python github_activity.py john-doe
```

---

## ✅ Example Output

```
Fetching GitHub activity for torvalds...

Recent activity for torvalds:

- Pushed 2 commits to torvalds/linux (on Jun 15, 2025 at 10:22)
- Opened issue 'Fix page fault handling' in john-doe/linux (on Jun 14, 2025 at 14:30)
...
```

---

## 🧠 What Your Readers Will Learn

- How to work with a public API
- How to parse and format JSON responses
- How to build CLI apps in Python
- How to handle errors gracefully
- How to format data with `datetime`

---

### Detailed Step by Step Explanation

[[Build a GitHub User Activity CLI Application - Explained Line by Line]]

