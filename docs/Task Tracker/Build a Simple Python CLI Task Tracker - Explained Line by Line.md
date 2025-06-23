Have you ever wanted to build a real-world Python project but didn't know where to start? In this post, I'll walk you through a Task Tracker CLI (Command-Line Interface) app written in Python. Not only will we build it, but we'll also break down how and why each part works - so you're not just copying code, but understanding it too.

Let's dive in:

---

## What You'll Build

A task manager you can use directly from the terminal to:
- Add tasks
- Update them
- Delete them
- Mark them as in progress or done
- List all tasks, or filter by status

And we'll do it all using Python + JSON - no databases or frameworks required.

---

## Project Setup

You need Python installed on your machine. Once that is done

1. Open VS Code or your preferred code editor.
2. Create a new directory:

```bash
mkdir task-tracker
cd task-tracker
```

3. Set up a virtual environment (Optional but recommended)

```bash
python -m venv venv
```

To activate it:
- On Windows:

```bash
source venv/Scripts/activate
```

- On Linux/macOS:

```bash
source venv/bin/activate
```

4. Create main python file.
	1. Inside VS Code, create the following file: `task_cli.py`



Now, let's look under the hood ⬇️

---

### **Setting Up the Projects

```python
import sys
import json
import os
from datetime import datetime
```

#### Explanation:

- `sys`: Used to access command-line arguments (e.g., `python task-cli.py add "Task description"`).
- `json`: Handles saving and loading tasks to a file in JSON format, allowing persistent storage.
- `os`: Helps check if the file exists (so the app doesn’t crash when it runs the first time).
- `datetime`: Used to timestamp when tasks are created or updated — gives the user context and tracking.

---

### **Loading and Saving Tasks

```python
TASKS_FILE = 'tasks.json'

def load_tasks():
    if not os.path.exists(TASKS_FILE):
        return []
    with open(TASKS_FILE, 'r') as f:
        try:
            return json.load(f)
        except json.JSONDecodeError:
            return []

def save_tasks(tasks):
    with open(TASKS_FILE, 'w') as f:
        json.dump(tasks, f, indent=2)
```

#### Explanation

`TASKS_FILE = 'tasks.json'`

To make the task tracker work even after the script exits, we need a way to **store data persistently**. That’s what this part of the code handles — by saving tasks in a file called `tasks.json`.

This sets the filename where all tasks will be stored. We’ll use this file to both **read** and **write** tasks.
Think of this file like a mini-database.

`load_tasks()`

This function **loads existing tasks** from `tasks.json` whenever the program starts.
Here’s what it does step-by-step:
```python
with open(TASKS_FILE, 'r') as f:
    try:
        return json.load(f)
```

- Opens the file in **read mode (`'r'`)**.
- Uses `json.load(f)` to **convert the JSON data back into Python objects** (a list of tasks).

```python
except json.JSONDecodeError:
    return []
```

- If the file exists but has invalid JSON (e.g., if someone accidentally edited it), it safely returns an empty list instead of crashing.

Why this matters: It makes the program fault-tolerant and user-friendly, especially for beginners who might mess with the JSON file manually.

---

### **Adding a Task

```python
def add_task(description):
    tasks = load_tasks()
    new_id = max([task['id'] for task in tasks], default=0) + 1
    now = datetime.now().isoformat()
    new_task = {
        'id': new_id,
        'description': description,
        'status': 'todo',
        'createdAt': now,
        'updatedAt': now
    }
    tasks.append(new_task)
    save_tasks(tasks)
    print(f"Task added successfully (id: {new_id})")
```

#### Explanation

The `add_task()` function is called when the user runs a command like:

```bash
python task-cli.py add "Buy groceries"
```

This is what adds a brand-new task to our to-do list.
Here's a breakdown:

```python
tasks = load_tasks()
```

- Loads the existing list of tasks from `tasks.json`.
- Ensures we don’t overwrite previous tasks when we add a new one.

```python
new_id = max([task['id'] for task in tasks], default=0) + 1
```

- Extracts all existing task IDs.
- Finds the **highest current ID** using `max(...)`.
- Adds 1 to ensure the new task has a unique ID.
- The `default=0` ensures it works even if the list is empty.

**Why this works:** It auto-increments IDs like a real database would.

```python
now = datetime.now().isoformat()
```

- Captures the current date and time in ISO format.
- This will be used to timestamp the task’s creation and last update.

Example output: `"2025-06-16T13:14:45.123456"`

```python
new_task = {
    'id': new_id,
    'description': description,
    'status': 'todo',
    'createdAt': now,
    'updatedAt': now
}
```

- Creates a dictionary (a single task object).
- Includes
	- `id`: Unique task ID
	- `description`: The task text (e.g., “Buy groceries”)
	- `status`: Starts as `'todo'`
	- `createdAt` & `updatedAt`: Both set to now

**Why this matters:** This ensures every task is well-structured and timestamped for tracking.

```python
tasks.append(new_task)
```

- Adds the new task to the existing list of tasks in memory.

```python
save_tasks(tasks)
```

- Persists the updated task list to the `tasks.json` file so it's not lost when the app closes.

```python
print(f"Task added successfully (id: {new_id})")
```

- Gives user feedback in the CLI, confirming the task was added and showing the task ID.

---

### **Updating a Task

```python
def update_task(task_id, new_description):
    tasks = load_tasks()
    for task in tasks:
        if task['id'] == task_id:
            task['description'] = new_description
            task['updatedAt'] = datetime.now().isoformat()
            save_tasks(tasks)
            print(f"Task {task_id} updated successfully")
            return
    print(f"Task {task_id} not found")
```

Sometimes you need to **edit a task** after creating it — maybe you want to reword it or clarify it. This function lets the user do that by running a command like:

```bash
python task-cli.py update 2 "Buy groceries and cook dinner"
```

#### Step by Step Explanation

```python
tasks = load_tasks()
```

- Load all existing tasks from the file so we can search through them.

```python
for task in tasks:
    if task['id'] == task_id:
```

- Loop through each task.
- Check if the task's ID matches the one the user wants to update.

**Why this works:** It ensures only the task with the exact matching ID gets updated.

```python
task['description'] = new_description
```

- Replaces the old description with the new one the user provided.

```python
task['updatedAt'] = datetime.now().isoformat()
```

- Updates the timestamp so users can see **when** the task was last modified.

This helps with task history and tracking changes.

```python
save_tasks(tasks)
```

- Saves the updated task list back to the file, including the newly edited description.

```python
print(f"Task {task_id} updated successfully")
return
```

- Lets the user know their update was successful and exits the function.

```python
print(f"Task {task_id} not found")
```

- If no task matches the provided ID, it notifies the user.
- This is good for user feedback and debugging typos in task IDs.

---

### **Deleting a Task

```python
def delete_task(task_id):
    tasks = load_tasks()
    for i, task in enumerate(tasks):
        if task['id'] == task_id:
            del tasks[i]
            save_tasks(tasks)
            print(f"Task {task_id} deleted successfully")
            return
    print(f"Task {task_id} not found")
```

This function allows the user to remove a task they no longer need using a command like:
```bash
python task-cli.py delete 3
```

Maybe the task is completed or was added by mistake — this function removes it from the list permanently.

#### Step by Step Explanation

```python
tasks = load_tasks()
```

- Loads the current list of tasks from the `tasks.json` file.

```python
for i, task in enumerate(tasks):
```

- Loops through all the tasks.
- `enumerate()` gives both:
	- `i`: the index (position in the list)
	- `task`: the actual task dictionary

**Why `enumerate` is useful here:** To directly remove a task from the list using its index.

```python
if task['id'] == task_id:
```

- Checks if the current task matches the ID the user wants to delete.

```python
del tasks[i]
```

- Deletes the task from the list using its index.

Using `del` is efficient and clean here — it removes the item without creating a new list.

```python
save_tasks(tasks)
```

- Saves the updated task list to disk so the deleted task is permanently removed.

```python
print(f"Task {task_id} deleted successfully")
return
```

- Provides feedback that the task was deleted, and then exits the function.

```python
print(f"Task {task_id} not found")
```

- If no task matches the given ID, the user is informed.
- This is helpful in case the user typed the wrong number or the task has already been deleted.

---

### **Changing Task Status

```python
def mark_task_status(task_id, status):
    tasks = load_tasks()
    for task in tasks:
        if task['id'] == task_id:
            task['status'] = status
            task['updatedAt'] = datetime.now().isoformat()
            save_tasks(tasks)
            print(f"Task {task_id} marked as {status}")
            return
    print(f"Task {task_id} not found")
```

Tasks evolve — they start as ideas (`todo`), move to `in-progress`, and eventually get `done`. This function lets your app reflect that progress.

Users can run:
```bash
python task-cli.py mark-in-progress 2
```

or

```bash
python task-cli.py mark-done 2
```


#### Step by Step Explanation

```python
tasks = load_tasks()
```

- Load the current list of tasks so we can find and modify the one we need.

```python
for task in tasks:
    if task['id'] == task_id:
```

- Search through all tasks by comparing their `id` to the user’s input.
- This ensures we're updating the right task.

```python
task['status'] = status
```

- Set the task’s `status` to the new value (e.g., `'in-progress'` or `'done'`).

**Why this is useful:** It allows the app to track the lifecycle of each task.

```python
task['updatedAt'] = datetime.now().isoformat()
```

- Update the `updatedAt` timestamp so users know when the task was last changed.

Example: `"2025-06-16T14:03:12.789654"`

```python
save_tasks(tasks)
```

- Save the modified task list so the status change persists after the program exits.

```python
print(f"Task {task_id} marked as {status}")
return
```

- Give the user confirmation that the status change was successful.

```python
print(f"Task {task_id} not found")
```

- If the ID doesn’t match any task, tell the user. This helps them correct mistakes like typing the wrong ID.

---

### **Listing Tasks

```python
def list_tasks(status_filter=None):
    tasks = load_tasks()
    if status_filter:
        filtered_tasks = [task for task in tasks if task['status'] == status_filter]
        if not filtered_tasks:
            print(f"No tasks with status '{status_filter}' found")
            return
        tasks = filtered_tasks
    
    for task in tasks:
        print(f"ID: {task['id']}")
        print(f"Description: {task['description']}")
        print(f"Status: {task['status']}")
        print(f"Created: {task['createdAt']}")
        print(f"Last Updated: {task['updatedAt']}")
        print("-" * 30)
```


This function is used to **view tasks**. Users can list all tasks or only those that match a specific status:

```bash
python task-cli.py list
```

or

```bash
python task-cli.py list done
```


#### Step by Step Explanation

```python
tasks = load_tasks()
```

- Load all saved tasks from the `tasks.json` file.

```python
if status_filter:
```

- Check if the user passed a filter (like `'done'`, `'todo'`, or `'in-progress'`).
- If not, show all tasks.

```python
filtered_tasks = [task for task in tasks if task['status'] == status_filter]
```

- Create a list of tasks where `task['status']` matches the filter.

This is a **list comprehension**, a Pythonic way to filter lists.

```python
if not filtered_tasks:
    print(f"No tasks with status '{status_filter}' found")
    return
```

- If the filtered list is empty, tell the user and stop.
- Prevents printing a blank list with no context.

```python
tasks = filtered_tasks
```

- Replace the full list with the filtered one, so the rest of the function prints only matching tasks.

```python
for task in tasks:
```

- Loop through the list of tasks (filtered or not) to display them.

```python
print(f"ID: {task['id']}")
print(f"Description: {task['description']}")
print(f"Status: {task['status']}")
print(f"Created: {task['createdAt']}")
print(f"Last Updated: {task['updatedAt']}")
print("-" * 30)
```

- Show each task's details in a human-readable format.
- The `"—" * 30` prints a divider for readability.

This helps users quickly scan through their tasks.

---

### **Handling User Commands

```python
def print_usage():
    print("Usage:")
    print("  task-cli add \"Task description\"")
    print("  task-cli update <task_id> \"New description\"")
    print("  task-cli delete <task_id>")
    print("  task-cli mark-in-progress <task_id>")
    print("  task-cli mark-done <task_id>")
    print("  task-cli list")
    print("  task-cli list todo|in-progress|done")

def main():
    if len(sys.argv) < 2:
        print_usage()
        return

    command = sys.argv[1].lower()

    if command == 'add' and len(sys.argv) >= 3:
        description = ' '.join(sys.argv[2:])
        add_task(description)
    elif command == 'update' and len(sys.argv) >= 4:
        try:
            task_id = int(sys.argv[2])
            new_description = ' '.join(sys.argv[3:])
            update_task(task_id, new_description)
        except ValueError:
            print("Invalid task ID")
    elif command == 'delete' and len(sys.argv) == 3:
        try:
            task_id = int(sys.argv[2])
            delete_task(task_id)
        except ValueError:
            print("Invalid task ID")
    elif command == 'mark-in-progress' and len(sys.argv) == 3:
        try:
            task_id = int(sys.argv[2])
            mark_task_status(task_id, 'in-progress')
        except ValueError:
            print("Invalid task ID")
    elif command == 'mark-done' and len(sys.argv) == 3:
        try:
            task_id = int(sys.argv[2])
            mark_task_status(task_id, 'done')
        except ValueError:
            print("Invalid task ID")
    elif command == 'list' and len(sys.argv) == 2:
        list_tasks()
    elif command == 'list' and len(sys.argv) == 3:
        status_filter = sys.argv[2].lower()
        if status_filter in ['todo', 'in-progress', 'done']:
            list_tasks(status_filter)
        else:
            print("Invalid status filter. Use 'todo', 'in-progress', or 'done'")
    else:
        print_usage()

if __name__ == '__main__':
    main()
```

This is the **brain** of your CLI app. It decides what action to perform based on user input.

#### Step by Step Explanation

`print-usage`

```python
def print_usage():
    print("Usage:")
    print("  task-cli add \"Task description\"")
    print("  task-cli update <task_id> \"New description\"")
    print("  task-cli delete <task_id>")
    print("  task-cli mark-in-progress <task_id>")
    print("  task-cli mark-done <task_id>")
    print("  task-cli list")
    print("  task-cli list todo|in-progress|done")
```


- Gives users a help guide when they enter the command incorrectly or don't know what to do.
- Super helpful for making your CLI-friendly.

`main()` - **Command Dispatcher**

This function uses `sys.argv` to read arguments passed in the terminal.

```python
if len(sys.argv) < 2:
    print_usage()
    return
```

- If no command is provided, show the usage instructions.


#### Command Matching

Each `elif` block matches a specific command and calls the appropriate function:

```python
command = sys.argv[1].lower()
```

`add`

```python
description = ' '.join(sys.argv[2:])
add_task(description)
```

- Joins the rest of the input into one string, in case the task description has spaces.

`update`

```python
task_id = int(sys.argv[2])
new_description = ' '.join(sys.argv[3:])
update_task(task_id, new_description)
```

- Converts the second argument to an integer ID.
- Joins the rest into the new task description.

`delete`

```python
task_id = int(sys.argv[2])
delete_task(task_id)
```

- Deletes a task by its ID.

`mark-in-progress/mark-done`

```python
mark_task_status(task_id, 'in-progress')
mark_task_status(task_id, 'done')
```

- Updates the task's `status` field.

`list`

```python
list_tasks()
list_tasks(status_filter)
```

- Shows all tasks or only those matching a status filter like `'todo'`.

#### Error Handling

Each command includes basic validation:
- Checks for valid input length
- Handles invalid task IDs using `try...except`
- Defaults to `print_usage()` for unknown commands

#### Putting It All Together

```python
if __name__ == '__main__':
    main()
```

This tells Python to run `main()` **only when the script is executed directly**, not when it’s imported as a module.