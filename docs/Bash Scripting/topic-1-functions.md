---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Creation date: "2026-10-27T11:40:15Z"
Created by:
    - Gallant Violin
Emoji: "\U0001F4B2"
id: bafyreieymtj36jcavlbilx6knv5l3corgyt3nrr6mqxuwk5jvjxwwt4dna
---
# Topic 1 : Functions   
🔥 Let's get into it! Welcome to T**opic 1: Functions in Bash.**   
We'll take this slowly. By the end of this lesson, you'll understand what functions are, how to create and call them, and why they're useful when writing real-world scripts.   
We'll use **Vim on your Fedora Linux system** for all practical exercises.   
# Topic 1: Functions in Bash   
## 1. Why we use functions   
Imagine you're writing a script that displays a welcome message.   
You might write:   
```
#!/usr/bin/env bash

echo "================================"
echo "Welcome to Bash Scripting!"
echo "Let's become Linux experts!"
echo "================================"

```
That works perfectly.   
Now imagine you want to display that same welcome message five times throughout your script.   
Would you copy and paste those four `echo` commands five times?   
You could, but that would make your script unnecessarily long and difficult to maintain.   
**Functions solve this problem by allowing you to group commands under a reusable name.**   
Instead of repeating the commands, you define them once and call the function whenever you need them.   
Think of a function as a small machine:   
- You give it a name.   
- You place instructions inside it.   
- You activate it when you need those instructions executed.   
   
For example, imagine having a function named `welcome`.   
Whenever you call `welcome`, it displays your welcome message.   
**The key benefit:** Write the instructions once, then reuse them whenever necessary.   
 --- 
## 2. How we use functions   
Let's build your understanding step by step.   
### A. Creating your first function   
Here is the basic syntax:   
```
function_name() {
    # Commands go here
}

```
Let's examine the components.   
|       Component    |                                                 Meaning    |
|:-----------------------|:---------------------------------------------------------------|
| `function_name`    |                        The name you give your function.    |
|            `()`    |                     Identifies the function definition.    |
|           `{ }`    |        Encloses the commands belonging to the function.    |
|        Commands    | The instructions the function will execute when called.    |

In Bash, you can also write `function function_name { ... }`, but we'll use the `function_name() { ... }` style because it's common and concise.   
Now let's create a working example.   
```
#!/usr/bin/env bash

welcome() {
    echo "================================"
    echo "Welcome to Bash Scripting!"
    echo "Let's become Linux experts!"
    echo "================================"
}

```
We've defined a function called `welcome`.   
**But here's something important: nothing will be displayed if you execute this script as it currently stands.**   
Why?   
Because we've only *defined* the function. We haven't called it.   
Think of defining a function as building a machine. Building the machine doesn't automatically switch it on.   
### B. Calling a function   
To execute a function, write its name followed by parentheses? No!   
In Bash, you call a function using **its name without parentheses**.   
Let's add a function call to our script.   
```
#!/usr/bin/env bash

welcome() {
    echo "================================"
    echo "Welcome to Bash Scripting!"
    echo "Let's become Linux experts!"
    echo "================================"
}

welcome

```
Look at the last line:   
```
welcome

```
That line calls the function.   
When Bash reaches it, the commands inside `welcome` execute.   
Expected output:   
```
================================
Welcome to Bash Scripting!
Let's become Linux experts!
================================

```
Notice the order:   
1. Bash reads the function definition.   
2. Bash reaches the `welcome` command.   
3. The function executes its commands.   
4. The messages appear in your terminal.   
   
**Remember this distinction: defining a function creates it; calling a function executes it.**   
### C. Calling the same function multiple times   
Now let's see where functions become particularly useful.   
Consider this script:   
```
#!/usr/bin/env bash

welcome() {
    echo "================================"
    echo "Welcome to Bash Scripting!"
    echo "Let's become Linux experts!"
    echo "================================"
}

welcome
welcome
welcome

```
We defined the function only once but called it three times.   
Expected output:   
```
================================
Welcome to Bash Scripting!
Let's become Linux experts!
================================
================================
Welcome to Bash Scripting!
Let's become Linux experts!
================================
================================
Welcome to Bash Scripting!
Let's become Linux experts!
================================

```
Every time Bash encounters `welcome`, it executes the function again.   
Imagine if the function contained 30 commands and you needed to execute them ten times. You wouldn't need to copy and paste those 30 commands ten times.   
You'd define the function once and call it ten times.   
That's the power of reusable code!   
### D. Your first real-world example   
Let's apply functions to something relevant to your Fedora environment.   
Suppose you frequently want to display information about your Linux system.   
You could create a function that displays your system information.   
```
#!/usr/bin/env bash

show_system_info() {
    echo "===== SYSTEM INFORMATION ====="
    echo "Username: $USER"
    echo "Home directory: $HOME"
    echo "Current directory: $PWD"
    echo "=============================="
}

show_system_info

```
Here, we've introduced three Bash variables:   
- $USER — The username associated with your current shell session.   
- $HOME — Your home directory.   
- $PWD — Your current working directory.   
   
Don't worry about memorizing variables yet. For this example, just observe how the function groups several related commands together.   
Expected output will resemble:   
```
===== SYSTEM INFORMATION =====
Username: ezra-yeswa
Home directory: /home/ezra-yeswa
Current directory: /home/ezra-yeswa/bash-practice
==============================

```
Your actual output will depend on your environment and the directory from which you run the script.   
This is a simple example of using a function to organize related operations.   
 --- 
## 3. Where we use functions   
Bash functions are useful whenever you have commands that belong together or need to be reused.   
Here are three common applications.   
### A. System administration   
You might create a function to display disk usage:   
```
show_disk_usage() {
    df -h
}

```
Calling `show_disk_usage` runs `df -h`, which displays disk-space information in a human-readable format.   
### B. Development workflows   
Suppose you're working on a software project and regularly need to display a message before running tests.   
```
prepare_tests() {
    echo "Preparing to run tests..."
    echo "Check that your test environment is ready."
}

```
You can call `prepare_tests` wherever you need those instructions.   
This example displays messages only; it doesn't actually prepare the testing environment.   
### C. Reusable maintenance operations   
You could define a function that checks the current date and displays it:   
```
show_date() {
    date
}

```
Whenever you call `show_date`, Bash executes the `date` command.   
These examples demonstrate a central principle: **functions help you organize scripts into meaningful, reusable units.**   
 --- 
# 4. Practice Zone 🧪   
Time to write some code yourself!   
You'll complete two exercises. Don't worry about making your solutions perfect on the first attempt. I'll review your code and help you improve it.   
## Task 1: Create a greeting function   
Create a file named `greeting.sh` using Vim.   
Your script must:   
1. Start with the Bash shebang.   
2. Define a function named `greet_user`.   
3. Display a welcome message inside the function.   
4. Display a second message explaining that you're learning Bash.   
5. Call the function once.   
   
**Expected output:**   
Your script should display two messages of your own choosing, one per line.   
**Hints:**   
- Define the function using `greet_user() { ... }`.   
- Place both `echo` commands inside the function.   
- Remember to call the function after defining it.   
   
Use Vim to create the file:   
```
cd ~/bash-practice
vim greeting.sh

```
Run your script using:   
```
bash greeting.sh

```
You don't need to make the script executable for this exercise because you're running it through Bash.   
## Task 2: Build a reusable Linux information function   
Create a file named `system-info.sh`.   
Your script must:   
1. Start with the Bash shebang.   
2. Define a function named `show_system_info`.   
3. Print a heading inside the function.   
4. Display your username using $USER.   
5. Display your home directory using $HOME.   
6. Call the function **twice**.   
   
**Expected behaviour:**   
Your system-information heading, username, and home directory should appear twice.   
For this task, use the same username and home-directory variables shown in our earlier example.   
Run your script using:   
```
bash system-info.sh

```
**Challenge:** Can you call the same function twice without repeating the commands inside its definition?   
 --- 
## 5. What to send me   
When you're finished, send me:   
1. The complete code for `greeting.sh`.   
2. The complete code for `system-info.sh`.   
3. The terminal output from both scripts.   
   
I'll check your syntax, function definitions, function calls, and output.   
If something goes wrong, we'll debug it together.   
**One final thing to remember:**   
A function definition is not the same as a function call. That's the most important concept in this lesson.   
Take your time. I'll wait for your solutions before we move on to the next topic. 💪   
   
 --- 
   
🔥 Excellent work! You're getting comfortable with Bash functions. Both scripts are structured correctly, and you've demonstrated that you understand how to define and call a function.   
Let's review them together.   
# 1. Code Review: greeting.sh   
### Your code   
```
#!/usr/bin/env bash

greet_user() {
        echo "Welcome to Linux World."
        echo "We're learning bash"
}

greet_user

```
### ✅ What you did right   
**1. Correct shebang**   
```
#!/usr/bin/env bash

```
This identifies Bash as the interpreter when the script is executed directly.   
**2. Correct function definition**   
```
greet_user() {
    echo "Welcome to Linux World."
    echo "We're learning bash"
}

```
You correctly used the function name, parentheses, and curly braces. Both `echo` commands are inside the function.   
**3. Correct function call**   
```
greet_user

```
You called the function after defining it. Bash executes the commands inside the function when it encounters this line.   
**Result: Task 1 passes!** ✅   
### A small improvement   
Your indentation works perfectly. However, two spaces are commonly used for indentation in Bash scripts. Consistent indentation makes larger scripts easier to read.   
For example:   
```
greet_user() {
  echo "Welcome to Linux World."
  echo "We're learning Bash."
}

```
This is a style preference, not a correction to an error.   
 --- 
# 2. Code Review: system\_info.sh   
### Your code   
```
#!/usr/bin/env bash

show_system_info() {
        echo "====== SYSTEM INFORMATION ====="
        echo "Username: $USER"
        echo "Home directory: $HOME"
        echo "Current directory: $PWD"
        echo "==============================="
}

show_system_info

```
### ✅ What you did right   
You correctly:   
- Defined `show_system_info`.   
- Used $USER to display the current username.   
- Used $HOME to display the home directory.   
- Used $PWD to display the current working directory.   
- Organized the output using headings and separators.   
- Called the function after defining it.   
   
Your use of variables is particularly good because the script can adapt to different users and working directories without hardcoding those values.   
For example, $HOME might expand to `/home/ezra-yeswa` on your Fedora system.   
### ⚠️ One requirement remains   
In Task 2, I asked you to call the function **twice**.   
Your current code calls it only once:   
```
show_system_info

```
To meet the requirement, add another function call:   
```
show_system_info
show_system_info

```
That's all you need to change!   
Notice that you don't need to copy the commands inside the function. You simply call the same function again.   
**This is one of the main advantages of functions: code reuse.**   
 --- 
# 3. An important concept to understand   
Let's examine what happens when Bash encounters these lines:   
```
show_system_info() {
  echo "System information"
}

show_system_info
show_system_info

```
Bash first reads the function definition, making the function available.   
The first `show_system_info` call executes the function.   
The second `show_system_info` call executes it again.   
The output is:   
```
System information
System information

```
The function is defined once but executed twice.   
This becomes increasingly valuable when functions contain many commands.   
For example, imagine a function with 20 commands that you need to execute five times. You can define those 20 commands once and call the function five times.   
That's much cleaner than writing the same commands repeatedly.   
 --- 
# 4. Your next step   
Before we move on, I want you to complete the second exercise fully.   
Make this one change to **`system_info.sh`:**   
Call `show_system_info` twice instead of once.   
Then run:   
```
bash system_info.sh

```
You should see the complete system-information section displayed twice.   
Send me your terminal output.   
Once we've verified that, we'll do a quick knowledge check on functions before moving to **Topic 2: Conditionals**.   
You're doing well. Keep going! 💪   
   
 --- 
   
Excellent! 🔥 You've corrected s`ystem_info.sh `and now call the function twice. Let's review your final version.   
## 1. Code Review: system\_info.sh   
Your code:   
```
#!/usr/bin/env bash

show_system_info() {
        echo "====== SYSTEM INFORMATION ====="
        echo "Username: $USER"
        echo "Home directory: $HOME"
        echo "Current directory: $PWD"
        echo "==============================="
}

show_system_info
show_system_info

```
### ✅ Requirements checklist   
|             Requirement    | Status    |                                 Explanation    |
|:-------------------------------|:--------------|:---------------------------------------------------|
|            Bash shebang    |      ✅    |            Correct interpreter declaration.    |
|     Function definition    |      ✅    |  `show_system_info()` is correctly defined.    |
|         Display heading    |      ✅    | Your heading separates the section clearly.    |
|        Display username    |      ✅    |                $USER supplies the username.    |
|  Display home directory    |      ✅    |          $HOME supplies the home directory.    |
| Call the function twice    |      ✅    |            You included two function calls.    |

**Result: Task 2 passed!** 🏆   
One small note: your indentation uses eight spaces. This works perfectly, although two or four spaces are often easier to maintain in larger scripts. There is no need to change it for correctness.   
## 2. What you've mastered   
You have demonstrated three fundamental skills:   
**A. Defining a function**   
```
show_system_info() {
    echo "System information"
}

```
This creates a reusable group of commands.   
**B. Calling a function**   
```
show_system_info

```
This executes the commands inside the function.   
**C. Reusing a function**   
```
show_system_info
show_system_info

```
This executes the same commands twice without duplicating their definitions.   
Remember: **define once, call whenever needed.**   
 --- 
## 3. Quick knowledge check 🧠   
Before we move to Topic 2, answer these two questions in your own words. No need to run any commands.   
**Question 1:** What is the difference between *defining* a function and *calling* a function?   
**Question 2:** Consider this script:   
```
#!/usr/bin/env bash

greet() {
    echo "Hello, Linux!"
}

```
If you execute this script exactly as written, will `Hello, Linux!` appear in the terminal? Explain why or why not.   
Once you answer these correctly, we'll move on to **Topic 2: Conditionals**, where you'll learn how to make Bash scripts make decisions. 💪   
   
 --- 
   
**Perfect! Both answers are correct. You're ready for the next topic!**   
Let's review your answers.   
### Question 1: Defining vs. calling a function   
Your answer is correct. You've understood the distinction.   
- **Defining a function** means creating a named block of commands that Bash can execute when requested.   
- **Calling a function** means instructing Bash to execute the commands inside that function.   
   
For example:   
```
# Definition
greet() {
    echo "Hello, Linux!"
}

# Call
greet

```
Notice that the function definition uses curly braces, while the function call uses only the function's name.   
### Question 2: Will the message appear?   
**Correct!** The message will not appear because the function has been defined but never called.   
This is an important principle: Bash doesn't automatically execute the commands inside a function just because it encounters its definition.   
You need to call the function explicitly.   
**Score: 2/2!** 🏆   
   
 --- 
   
# Topic 2: Conditionals in Bash   
Now we're moving to the second topic on your curriculum: **Conditionals**.   
We'll follow our usual structure:   
1. Why we use it.   
2. How we use it.   
3. Where we use it.   
4. Two practical tasks.   
   
We'll focus on the `if` statement first. We won't cover `case` statements or other conditional structures yet.   
