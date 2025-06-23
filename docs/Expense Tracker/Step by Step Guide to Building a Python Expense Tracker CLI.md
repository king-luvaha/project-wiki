
In this tutorial, we’ll build a **command-line expense tracker** using Python. This tool will help users log expenses, set budgets, and generate spending reports. By the end, you’ll understand how to structure a CLI application, manage data persistence, and implement key financial tracking features.

---

## **Step 1: Planning the Application**

Before writing code, let’s outline what our expense tracker will do:

### **Core Features**

1. **Add Expenses** (description, amount, category, date)
2. **List Expenses** (filter by category)
3. **Delete/Update Expenses**
4. **Set Monthly Budgets**
5. **Generate Reports** (monthly summary, category-wise breakdown)
6. **Export Data** (to CSV)

### **Data Storage**

- **Expenses**: Stored in `expenses.json`
- **Budgets**: Stored in `budgets.json`

### **Tools & Libraries**

- `argparse` – For CLI argument parsing  
- `json` – For storing/loading data  
- `csv` – For exporting expenses  
- `datetime` – For tracking expense dates  

---

## **Step 2: Setting Up the Project**

Create a new Python file (`expense_tracker.py`) and start with the imports:

```python
import argparse
import json
import os
from datetime import datetime
import csv
from collections import defaultdict
```

### **Why These Imports?**

- `argparse` → Handles command-line inputs  
- `json` → Saves/loads expenses in JSON format  
- `os` → Checks if data files exist  
- `datetime` → Automatically logs expense dates  
- `csv` → Exports data to CSV  
- `defaultdict` → Helps with category-wise summaries  

---

## **Step 3: Defining Data Storage**

We need two files:
1. `expenses.json` → Stores all expenses  
2. `budgets.json` → Stores monthly budgets  

### **Helper Functions to Load/Save Data**

```python
EXPENSES_FILE = "expenses.json"
BUDGET_FILE = "budgets.json"

def load_expenses():
    """Load expenses from file or return empty list if file doesn't exist"""
    if os.path.exists(EXPENSES_FILE):
        with open(EXPENSES_FILE, "r") as f:
            return json.load(f)
    return []

def save_expenses(expenses):
    """Save expenses to file"""
    with open(EXPENSES_FILE, "w") as f:
        json.dump(expenses, f, indent=2)

def load_budgets():
    """Load budgets from file or return empty dict if file doesn't exist"""
    if os.path.exists(BUDGET_FILE):
        with open(BUDGET_FILE, "r") as f:
            return json.load(f)
    return {}

def save_budgets(budgets):
    """Save budgets to file"""
    with open(BUDGET_FILE, "w") as f:
        json.dump(budgets, f, indent=2)
```

### **Why This Works**

- Checks if files exist before loading to avoid errors.  
- Returns empty structures (`[]` or `{}`) if files don’t exist yet.  
- Uses `json.dump()` with `indent=2` for readable JSON files.  

---

## **Step 4: Adding Expenses**
### **Function Definition**

```python
def add_expense(args):
    """Add a new expense"""
    expenses = load_expenses()
    
    # Generate a new ID (1 + highest existing ID)
    new_id = max([expense["id"] for expense in expenses] or [0]) + 1
    
    expense = {
        "id": new_id,
        "date": datetime.now().strftime("%Y-%m-%d"),  # Auto-log date
        "description": args.description,
        "amount": float(args.amount),
        "category": args.category
    }
    
    expenses.append(expense)
    save_expenses(expenses)
    
    # Check if budget is exceeded
    current_month = datetime.now().month
    budgets = load_budgets()
    
    if str(current_month) in budgets:
        monthly_expenses = sum(
            float(e["amount"]) for e in expenses 
            if datetime.strptime(e["date"], "%Y-%m-%d").month == current_month
        )
        if monthly_expenses > float(budgets[str(current_month)]):
            print(f"Warning: Budget exceeded! (Budget: ${budgets[str(current_month)]}, Spent: ${monthly_expenses})")
    
    print(f"Expense added (ID: {new_id})")
```

### **Key Points**

- Auto-generates an `id` for each expense.  
- Uses `datetime.now()` to log the current date.  
- Checks if the expense exceeds the monthly budget.  

---

## **Step 5: Listing Expenses**
### **Function Definition**

```python
def list_expenses(args):
    """List all expenses, optionally filtered by category"""
    expenses = load_expenses()
    
    if not expenses:
        print("No expenses found.")
        return
    
    if args.category:  # Filter by category if provided
        expenses = [e for e in expenses if e["category"] == args.category]
        if not expenses:
            print(f"No expenses in category '{args.category}'.")
            return
    
    # Print formatted table
    print(f"{'ID':<5} {'Date':<12} {'Description':<20} {'Amount':<10} {'Category':<15}")
    print("-" * 65)
    for expense in expenses:
        print(f"{expense['id']:<5} {expense['date']:<12} {expense['description'][:18]:<20} ${expense['amount']:<9.2f} {expense['category'][:13]:<15}")
```

### **Why This Works**

- Uses string formatting (`:<10`) for aligned columns.  
- Filters expenses if `--category` is provided.  
- Handles empty expense lists gracefully.  

---

## **Step 6: Deleting & Updating Expenses**
### **Delete Function**

```python
def delete_expense(args):
    """Delete an expense by ID"""
    expenses = load_expenses()
    
    for i, expense in enumerate(expenses):
        if expense["id"] == args.id:
            del expenses[i]
            save_expenses(expenses)
            print("Expense deleted successfully")
            return
    
    print(f"Error: Expense ID {args.id} not found.")
```

### **Update Function**

```python
def update_expense(args):
    """Update an existing expense"""
    expenses = load_expenses()
    
    for expense in expenses:
        if expense["id"] == args.id:
            if args.description:
                expense["description"] = args.description
            if args.amount:
                expense["amount"] = float(args.amount)
            if args.category:
                expense["category"] = args.category
            save_expenses(expenses)
            print("Expense updated successfully")
            return
    
    print(f"Error: Expense ID {args.id} not found.")
```

### **Key Points**

- Both functions search by `id` and modify the JSON file.  
- Only updates fields that are provided (`args.description`, `args.amount`, etc.).  

---

## **Step 7: Budget Tracking & Reports**
### **Set Budget Function**

```python
def set_budget(args):
    """Set budget for a specific month"""
    budgets = load_budgets()
    budgets[str(args.month)] = float(args.amount)
    save_budgets(budgets)
    month_name = datetime.strptime(f"2024-{args.month}-01", "%Y-%m-%d").strftime("%B")
    print(f"Budget for {month_name} set to ${args.amount}")
```

### **Summary Function**

```python
def summary(args):
    """Show summary of expenses, optionally for a specific month"""
    expenses = load_expenses()
    
    if not expenses:
        print("No expenses found.")
        return
    
    if args.month:
        month_expenses = [
            float(e["amount"]) for e in expenses 
            if datetime.strptime(e["date"], "%Y-%m-%d").month == args.month
        ]
        total = sum(month_expenses)
        month_name = datetime.strptime(f"2024-{args.month}-01", "%Y-%m-%d").strftime("%B")
        print(f"Total expenses for {month_name}: ${total:.2f}")
        
        # Compare with budget
        budgets = load_budgets()
        if str(args.month) in budgets:
            budget = float(budgets[str(args.month)])
            print(f"Budget: ${budget:.2f}")
            if total > budget:
                print(f"Overspent by ${total - budget:.2f}")
            else:
                print(f"Remaining budget: ${budget - total:.2f}")
    else:
        total = sum(float(e["amount"]) for e in expenses)
        print(f"Total expenses: ${total:.2f}")
```

### **Key Features**

- Shows spending vs. budget.  
- Handles both monthly and overall summaries.  

---

## **Step 8: Exporting & Category Reports**
### **Export to CSV**

```python
def export_to_csv(args):
    """Export expenses to CSV"""
    expenses = load_expenses()
    if not expenses:
        print("No expenses to export.")
        return
    
    filename = args.filename or "expenses_export.csv"
    
    with open(filename, "w", newline="") as csvfile:
        writer = csv.DictWriter(csvfile, fieldnames=["id", "date", "description", "amount", "category"])
        writer.writeheader()
        writer.writerows(expenses)
    
    print(f"Exported to {filename}")
```

### **Category Summary**

```python
def category_summary(args):
    """Show expenses by category"""
    expenses = load_expenses()
    category_totals = defaultdict(float)
    
    for expense in expenses:
        category_totals[expense["category"]] += float(expense["amount"])
    
    print("Expenses by Category:")
    print("-" * 30)
    for category, total in sorted(category_totals.items(), key=lambda x: x[1], reverse=True):
        print(f"{category:<20}: ${total:.2f}")
```

### **Why This Works**

- Uses `csv.DictWriter` for clean CSV exports.  
- `defaultdict` simplifies category-wise summation.  

---

## **Step 9: Building the CLI with `argparse`**

```python
def main():
    parser = argparse.ArgumentParser(description="Expense Tracker CLI")
    subparsers = parser.add_subparsers(dest="command", required=True)
    
    # Add command
    add_parser = subparsers.add_parser("add", help="Add a new expense")
    add_parser.add_argument("--description", required=True, help="Expense description")
    add_parser.add_argument("--amount", required=True, type=float, help="Expense amount")
    add_parser.add_argument("--category", default="Uncategorized", help="Expense category")
    add_parser.set_defaults(func=add_expense)
    
    # List command
    list_parser = subparsers.add_parser("list", help="List expenses")
    list_parser.add_argument("--category", help="Filter by category")
    list_parser.set_defaults(func=list_expenses)
    
    # Delete command
    delete_parser = subparsers.add_parser("delete", help="Delete an expense")
    delete_parser.add_argument("--id", type=int, required=True, help="Expense ID to delete")
    delete_parser.set_defaults(func=delete_expense)
    
    # Update command
    update_parser = subparsers.add_parser("update", help="Update an expense")
    update_parser.add_argument("--id", type=int, required=True, help="Expense ID to update")
    update_parser.add_argument("--description", help="New description")
    update_parser.add_argument("--amount", type=float, help="New amount")
    update_parser.add_argument("--category", help="New category")
    update_parser.set_defaults(func=update_expense)
    
    # Summary command
    summary_parser = subparsers.add_parser("summary", help="Show summary")
    summary_parser.add_argument("--month", type=int, choices=range(1, 13), help="Month (1-12)")
    summary_parser.set_defaults(func=summary)
    
    # Budget command
    budget_parser = subparsers.add_parser("set-budget", help="Set monthly budget")
    budget_parser.add_argument("--month", type=int, choices=range(1, 13), required=True, help="Month (1-12)")
    budget_parser.add_argument("--amount", type=float, required=True, help="Budget amount")
    budget_parser.set_defaults(func=set_budget)
    
    # Export command
    export_parser = subparsers.add_parser("export", help="Export to CSV")
    export_parser.add_argument("--filename", help="Output filename (default: expenses_export.csv)")
    export_parser.set_defaults(func=export_to_csv)
    
    # Category summary
    category_parser = subparsers.add_parser("category-summary", help="Show category breakdown")
    category_parser.set_defaults(func=category_summary)
    
    args = parser.parse_args()
    args.func(args)

if __name__ == "__main__":
    main()
```

### **Key Takeaways**

- Uses `subparsers` for different commands (`add`, `list`, `delete`, etc.).  
- Each subparser has its own arguments.  
- `set_defaults(func=...)` maps commands to their functions.  

---

## **Final Thoughts**

This CLI tool:
✔️ Tracks expenses & budgets  
✔️ Exports data to CSV  
✔️ Provides spending insights  
✔️ Is extensible (can add more features like charts, recurring expenses, etc.)  

---

## **How to Run the Expense Tracker CLI: Complete Command Guide**

Here's a full list of commands to use the expense tracker, covering all features:
### **1. Adding an Expense**

Log a new expense with description, amount, and category (default: "Uncategorized").

```sh
python expense_tracker.py add --description "Dinner" --amount 25.50 --category "Food"
```
*(Category is optional; defaults to "Uncategorized")*

---

### **2. Listing Expenses**

 **List all expenses**

```sh
python expense_tracker.py list
```

 **Filter by category (e.g., "Food")**

```sh
python expense_tracker.py list --category "Food"
```

---

### **3. Deleting an Expense**

Remove an expense by its ID (check ID from `list` command).

```sh
python expense_tracker.py delete --id 3
```

---

### **4. Updating an Expense**

Modify an existing expense (update only provided fields).

```sh
# Update description only
python expense_tracker.py update --id 3 --description "Lunch with friends"

# Update amount and category
python expense_tracker.py update --id 3 --amount 30.00 --category "Dining"

# Update all fields
python expense_tracker.py update --id 3 --description "Uber Ride" --amount 15.75 --category "Transport"
```

---

### **5. Setting a Monthly Budget**

Set a budget for a specific month (1-12, e.g., `6` = June).

```sh
python expense_tracker.py set-budget --month 6 --amount 1000
```

---

### **6. Viewing Summaries**

 **Total expenses (all time)**

```sh
python expense_tracker.py summary
```

 **Monthly summary (e.g., June)**

```sh
python expense_tracker.py summary --month 6
```

*Output includes:*
- Total spending for the month  
- Budget comparison (if set)  
- Warnings if overspent  

---

### **7. Category-wise Spending Breakdown**

See totals grouped by category (sorted highest to lowest).

```sh
python expense_tracker.py category-summary
```

*Example output:*

```
Food               : $450.00  
Transport          : $120.50  
Entertainment      : $85.25  
Uncategorized      : $30.00  
```

---

### **8. Exporting Expenses to CSV**

Save all expenses to a CSV file (default: `expenses_export.csv`).

```sh
# Default filename
python expense_tracker.py export

# Custom filename
python expense_tracker.py export --filename "my_expenses_june.csv"
```

---

### **Example Workflow**

1. **Set a June budget**:
   ```sh
   python expense_tracker.py set-budget --month 6 --amount 500
   ```

2. **Add expenses**:
   ```sh
   python expense_tracker.py add --description "Groceries" --amount 75 --category "Food"
   python expense_tracker.py add --description "Gas" --amount 40 --category "Transport"
   ```

3. **Check spending vs. budget**:
   ```sh
   python expense_tracker.py summary --month 6
   ```

4. **Export data**:
   ```sh
   python expense_tracker.py export --filename "june_expenses.csv"
   ```


----

### **Next Steps**

- Add a GUI (Tkinter, PyQt)  
- Integrate with banking APIs  
- Add data visualization (Matplotlib)  

---

### **Detailed Step by Step Explanation

[[Build an Expense Tracker CLI Application - Explained Line by Line]]

