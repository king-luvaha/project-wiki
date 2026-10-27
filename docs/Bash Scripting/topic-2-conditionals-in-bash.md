---
# yaml-language-server: $schema=schemas/page.schema.json
Object type:
    - Page
Creation date: "2026-10-27T15:27:38Z"
Created by:
    - Gallant Violin
Emoji: "\U0001F4B2"
Cover image or color: bafyreicfxhc7tuhg3l7j7vo53af2sy4wysn7jvra4mv6uwdvsdprfsgodi
id: bafyreia3nvbn5ez6m3flv3tb4cu6lcldt2lxiwo7fehakb6m3icgcuy5za
---
# Topic 2: Conditionals in Bash   
# Topic 2: Conditionals in Bash   
Now we're moving to the second topic on your curriculum: **Conditionals**.   
We'll follow our usual structure:   
1. Why we use it.   
2. How we use it.   
3. Where we use it.   
4. Two practical tasks.   
   
We'll focus on the `if` statement first. We won't cover `case` statements or other conditional structures yet.   
## 1. Why we use conditionals   
Imagine writing a Bash script that checks whether a file exists.   
Without conditional logic, your script might simply execute a command regardless of whether the file exists.   
But what if you want the script to behave differently depending on the situation?   
For example:   
- If a file exists, display a success message.   
- If a file doesn't exist, display a warning.   
   
**Conditionals allow your script to make decisions based on whether a condition is true or false.**   
Think of a conditional as a question your script asks before deciding what to do.   
For example:   
> Does the file `hello.sh` exist?   

- If yes, display `File found!`   
- If no, display `File not found!`   
   
This makes scripts more intelligent and useful.   
## 2. How we use conditionals   
### A. Your first if statement   
Consider this example:   
```
#!/usr/bin/env bash

if [ -f "hello.sh" ]; then
    echo "File found!"
fi

```
Let's break it down.   
|            Component   <br /> |                                                      Meaning   <br /> |
|:----------------------------|:--------------------------------------------------------------------|
|                 `if`   <br /> |                              Starts a conditional statement.   <br /> |
|  `[ -f "hello.sh" ]`   <br /> |          Checks whether `hello.sh` exists as a regular file.   <br /> |
|               `then`   <br /> | Introduces the commands to execute if the condition is true.   <br /> |
| `echo "File found!"`   <br /> |                       Displays a message if the file exists.   <br /> |
|                 `fi`   <br /> |                              Ends the conditional statement.   <br /> |

Notice that `fi` is `if` spelled backward.   
**Important:** The spaces inside `[ ... ]` are required.   
Correct:   
```
[ -f "hello.sh" ]

```
Incorrect:   
```
[-f "hello.sh"]

```
The second example doesn't use the required spacing, so Bash won't interpret it as intended.   
### B. Run your first conditional   
Since you've already created `hello`, let's practise with a file you know exists.   
Create a new script:   
```
cd ~/bash-practice
vim check-file.sh

```
Inside Vim, enter:   
```
#!/usr/bin/env bash

if [ -f "hello" ]; then
    echo "The file exists!"
fi

```
Save and exit Vim.   
Run the script from your practice directory:   
```
bash check-file.sh

```
If `hello` exists in that directory, you'll see:   
```
The file exists!

```
If it doesn't exist, the script will finish without displaying anything.   
Why? Because the `echo` command executes only when the condition is true.   
### C. Introducing else   
What if we want the script to display a message in both situations?   
We can use `else`.   
```
#!/usr/bin/env bash

if [ -f "hello" ]; then
    echo "The file exists!"
else
    echo "The file does not exist!"
fi

```
Now there are two possible outcomes.   
**When the file exists:**   
```
The file exists!

```
**When the file doesn't exist:**   
```
The file does not exist!

```
Bash evaluates the condition and executes the appropriate branch.   
The structure is:   
```
if [ condition ]; then
    # Commands executed when the condition is true
else
    # Commands executed when the condition is false
fi

```
For now, remember these three keywords:   
- `if` starts the decision.   
- `else` provides an alternative.   
- `fi` ends the decision.   
   
We'll explore more types of conditions in later lessons.   
## 3. Where we use conditionals   
Conditionals are used extensively in real-world Bash scripts.   
**Example 1: Checking whether a directory exists**   
```
if [ -d "$HOME/bash-practice" ]; then
    echo "Practice directory found!"
else
    echo "Practice directory is missing!"
fi

```
Here, `-d` checks whether the specified path is a directory.   
**Example 2: Checking whether a command is available**   
```
if command -v git >/dev/null 2>&1; then
    echo "Git is installed!"
else
    echo "Git is not available!"
fi

```
This checks whether Bash can locate the `git` command.   
The expression `>/dev/null 2>&1` suppresses the command's output, allowing the script to focus on which branch should run. We'll study input/output redirection separately later.   
**Example 3: Checking before performing an operation**   
A maintenance script might check whether a backup file exists before attempting to process it.   
This helps prevent errors and allows the script to respond appropriately to different situations.   
These examples show why conditionals are essential for writing reliable automation scripts.   
 --- 
# 4. Practice Zone 🧪   
Time to put your knowledge into practice!   
Complete both tasks using **Vim on Fedora**.   
### Task 1: Check whether a file exists   
Create a script named `file-checker.sh`.   
Your script must:   
1. Start with the Bash shebang.   
2. Check whether a file named `hello` exists in the current directory.   
3. If it exists, display `Great! The file exists.`   
4. If it doesn't exist, display `Oops! The file was not found.`   
   
**Requirements:**   
- Use `if`, `else`, and `fi`.   
- Use the `-f` file test.   
- Use `echo` to display the messages.   
   
Test it from your practice directory:   
```
cd ~/bash-practice
vim file-checker.sh

```
Then run:   
```
bash file-checker.sh

```
**Challenge:** Rename `hello` temporarily or test against a filename that doesn't exist. Observe how the output changes, then restore anything you renamed.   
### Task 2: Check whether your practice directory exists   
Create a script named `directory-checker.sh`.   
Your script must:   
1. Start with the Bash shebang.   
2. Check whether the directory `~/bash-practice` exists.   
3. If it exists, display `Your Bash practice directory is ready!`   
4. Otherwise, display `Your Bash practice directory is missing!`   
   
**Requirements:**   
- Use `if`, `else`, and `fi`.   
- Use the `-d` directory test.   
- Use `echo` to display the messages.   
   
Run it using:   
```
bash directory-checker.sh

```
**Important:** For Task 2, use the full path `"$HOME/bash-practice"` inside the condition. This makes the check work regardless of your current directory.   
 --- 
## 5. Your submission   
When you've completed both tasks, send me:   
1. The code for `file-checker.sh`.   
2. The code for `directory-checker.sh`.   
3. The terminal output from both scripts.   
   
I'll review your solutions, explain any mistakes, and help you correct them.   
**We won't move to the next topic until you've completed these exercises and are comfortable with conditionals.** 💪   
   
 --- 
   
   
Excellent! 💪 You've corrected f`ile-checker.sh `by adding the Bash shebang. Let's do the final review.   
## 1. file-checker.sh — ✅ Passed   
Your code:   
```
#!/usr/bin/env bash

if [ -f "hello" ]; then
        echo "Great! The file exists."
else
        echo "Oops! The file was not found."
fi

```
**Everything is correct!**   
You've demonstrated that you understand how to:   
- Use `-f` to check whether a regular file exists.   
- Use `if` to evaluate a condition.   
- Use `else` to handle the alternative outcome.   
- Use `fi` to close the conditional.   
   
One important detail: `"hello"` is a relative path, so the script checks for that file in the **current working directory**, not necessarily the directory where the script is saved.   
Since you're practising in `~/bash-practice`, that works as intended when you run the script from that directory.   
## 2. directory-checker.sh — ✅ Passed   
Your code:   
```
#!/usr/bin/env bash

# Check directory exists
if [ -d "$HOME/bash-practice" ]; then
        echo "Your Bash practice directory is ready!"
else
        echo "Your Bash practice directory is missing!"
fi

```
This is also correct!   
You've demonstrated that you understand the difference between these two file tests:   
| Test   <br /> |                               Purpose   <br /> |
|:------------|:---------------------------------------------|
| `-f`   <br /> | Checks whether a regular file exists.   <br /> |
| `-d`   <br /> |    Checks whether a directory exists.   <br /> |

You also used `"$HOME/bash-practice"` correctly to construct the directory path.   
**Both scripts pass their code reviews.** 🏆   
One final step: if you haven't already, execute both scripts and share their output so we can confirm their behaviour on your Fedora system.   
 --- 
## 3. Quick knowledge check 🧠   
Before we finish this topic, answer these two questions.   
**Question 1:** In the following condition, what does `-f` check?   
```
if [ -f "hello" ]; then
    echo "File found!"
fi

```
**Question 2:** Suppose the directory `/home/ezra-yeswa/bash-practice` exists, but the file `missing.txt` does not.   
What will happen when you run this script?   
```
#!/usr/bin/env bash

if [ -f "$HOME/missing.txt" ]; then
    echo "File found!"
else
    echo "File not found!"
fi

```
Explain which message will appear and why.   
Once you've answered, we'll review your understanding and then move forward when you're ready. 🚀   
   
 --- 
   
   
Excellent! 🔥 B**oth answers are correct. Score: 2/2! **🏆   
Let's review them.   
### Question 1: What does -f check?   
Your answer: *It checks if a file exists.*   
✅ Correct! More precisely, `-f` checks whether a path exists and refers to a **regular file**.   
For example:   
```
if [ -f "hello" ]; then
    echo "File found!"
fi

```
If `hello` is a regular file in the current working directory, the condition is true.   
### Question 2: What happens when missing.txt doesn't exist?   
Your answer: *File not found will be displayed because it is missing.*   
✅ Correct!   
Bash evaluates the condition:   
```
[ -f "$HOME/missing.txt" ]

```
Since the file doesn't exist, the condition is false. Bash executes the `else` branch:   
```
File not found!

```
You've understood how Bash uses conditions to choose which commands to execute.   
 --- 
# 🎓 Topic 2 Complete: Conditionals   
You've successfully completed our introductory lessons on conditionals.   
You now understand:   
- `if` — starts a conditional.   
- `then` — introduces commands to execute when the condition is true.   
- `else` — provides an alternative when the condition is false.   
- `fi` — closes the conditional.   
- `-f` — checks for a regular file.   
- `-d` — checks for a directory.   
   
You've also written scripts that apply these concepts to real situations on Fedora.   
**One important distinction:** `-f` doesn't check whether something is merely present in the filesystem; it checks whether the specified path refers to a regular file. Similarly, `-d` checks whether a path refers to a directory.   
Keep that distinction in mind as we build more advanced scripts.   
 --- 
## 🚀 Ready for Topic 3: For Loops?   
Your next topic, following your curriculum, is **For Loops**.   
We'll learn how to repeat commands automatically without writing them over and over.   
For example, imagine displaying a message five times. Instead of writing five separate `echo` commands, you could use a loop.   
We'll cover only the fundamentals of `for` loops, following our usual structure:   
1. **Why we use it**   
2. **How we use it**   
3. **Where we use it**   
4. **Practice Zone:** Two practical tasks   
   
We'll continue using Vim and Fedora for our exercises.   
**Are you ready to begin For Loops?** 💻   
