
In this tutorial, we’ll build a **Task Tracker CLI Application** in Python that allows users to manage tasks from the command line. The app will support adding, updating, deleting, and tracking task statuses (todo, in-progress, done), with all data stored in a JSON file.

---

## **Step 1: Planning the Application**
Before writing code, let’s outline the key features and structure:

### **Features:**
1. **Add tasks** with descriptions.
2. **Update** existing tasks.
3. **Delete** tasks.
4. **Mark tasks** as *in-progress* or *done*.
5. **List tasks** (all or filtered by status).
6. **Persistent storage** using JSON.

### **Task Structure (JSON Schema):**
Each task will have:
- `id` (unique identifier)
- `description` (task details)
- `status` (`todo`, `in-progress`, `done`)
- `createdAt` (timestamp when created)
- `updatedAt` (timestamp when last modified)

---

## **Step 2: Setting Up the Project**
1. **Create a new Python file** (e.g., `task_cli.py`).
2. **Import required modules**:
   ```python
   import sys
   import json
   import os
   from datetime import datetime
   ```

---

## **Step 3: Core Functionality**
### **1. Loading and Saving Tasks**
We need functions to:
- **Load tasks** from `tasks.json` (or create it if missing).
- **Save tasks** back to the file.

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

---

### **2. Adding a Task**
- Takes a **description** as input.
- Assigns a new **auto-incremented ID**.
- Sets default status (`todo`) and timestamps.

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

---

### **3. Updating a Task**
- Takes a **task ID** and **new description**.
- Updates the task if found.

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

---

### **4. Deleting a Task**
- Removes a task by **ID**.

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

---

### **5. Changing Task Status**
- Marks tasks as **in-progress** or **done**.

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

---

### **6. Listing Tasks**
- Lists all tasks or filters by status (`todo`, `in-progress`, `done`).

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

---

## **Step 4: Handling User Commands**
We’ll parse command-line arguments to execute the right function.

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

---

## **Step 5: Testing the Application**
Run commands like:
```bash
python task_cli.py add "Buy groceries"
python task_cli.py update 1 "Buy milk and eggs"
python task_cli.py mark-done 1
python task_cli.py list done
```

---

## **Conclusion**
We’ve built a **fully functional CLI Task Tracker** with:
✔ **Add/Update/Delete** tasks  
✔ **Track status** (todo, in-progress, done)  
✔ **Persistent JSON storage**  
✔ **Easy command-line usage**  


---

## Detailed Step by Step Explanation

[[Build a Simple Python CLI Task Tracker - Explained Line by Line]]

