## **Step 1: Import Required Modules**

Let’s start with the imports:

```python
import random
import time 
from collections import defaultdict
```

---

### 🔍 **Line-by-Line Explanation**

**1. `import random`**

✅ **What it does:**  
This line imports Python’s built-in `random` module, which allows your program to generate random numbers or make random selections.

💡 **Why it’s used here:**  
In your game, the computer needs to **think of a random number between 1 and 100**, and that’s exactly what this module helps with.

📌 **Example usage:**

```python
random_number = random.randint(1, 100)  # Picks a random number between 1 and 100
```

---

**2. `import time`**

✅ **What it does:**  
This imports the `time` module, which provides various time-related functions.

💡 **Why it’s used here:**  
You’re tracking **how long it takes** a player to guess the number. So, you’ll:

- Record the start time when the game begins
- Record the end time when the player finishes
- Subtract them to get the total time taken

📌 **Example usage:**

```python
start = time.time()
# ... game logic ...
end = time.time()
print("Time taken:", end - start)
```

---

 **3. `from collections import defaultdict`**

✅ **What it does:**  
This line imports `defaultdict` from the `collections` module — a powerful tool for working with dictionaries.

💡 **Why it’s used here:**  
A regular Python dictionary throws an error if you try to access a key that doesn't exist. `defaultdict` solves that by **automatically assigning a default value** to new keys.

📌 **In your code:**

```python
high_scores = defaultdict(int)
```

This means:

- `high_scores[1]` will default to `0` if not yet set.
- You don’t need to check if the key exists before using it.

🧠 Think of it as a **dictionary with a built-in safety net**.

---

### Summary for Your Blog:

 - `random` to generate a secret number for the player to guess,
 - `time` to measure how fast they guess it, and
 - `defaultdict` to safely store and track high scores for each difficulty level. 

---

## **Step 2: Display the Welcome Screen**

Perfect — this section defines the **welcome message** and sets the stage for the player. Let’s break it down **line by line**, clearly and in a way your blog readers will understand.

---

## 🔹 Code Snippet

```python
def display_welcome():
    print("\033[1;36m\nWelcome to the Number Guessing Game!\033[0m")
    print("I'm thinking of a number between 1 and 100. 🧠")
    print("\nDifficulty levels:")
    print("1. Easy (10 chances)")
    print("2. Medium (5 chances)")
    print("3. Hard (3 chances)")
```

---

## 🔍 Explanation (Line-by-Line)


  `def display_welcome():`

✅ **What it does:**  
This line defines a **function** named `display_welcome`. In Python, functions are blocks of reusable code. You can call this function whenever you want to show the welcome message.

💡 **Why it's used:**  
To keep the code organized. Instead of repeating the welcome message every time, we define it once and just call `display_welcome()`.

---

 🟦 `print("\033[1;36m\nWelcome to the Number Guessing Game!\033[0m")`

✅ **What it does:**  
This prints the welcome message with **colored text** in the terminal. Specifically:

- `\033[1;36m` starts **cyan-colored bold text**.
- `\n` adds a line break before the message.
- `\033[0m` resets the text back to normal color after the message.

🎨 **Terminal Color Codes:**  
These are **ANSI escape codes** that change the appearance of printed text in the terminal. They work in many terminal environments (like VS Code, Git Bash, Linux terminals).


---

 🟦 `print("I'm thinking of a number between 1 and 100. 🧠")`

✅ **What it does:**  
This line gives a **hint** about the rules of the game: the number the player must guess is between 1 and 100.

🧠 **Emoji Tip:** Emojis like `🧠` can add personality to a CLI (command-line interface) app. It feels more fun and modern.

---

 🟦 `print("\nDifficulty levels:")`

✅ **What it does:**  
Introduces the player to the different difficulty levels. The `\n` adds a line break before the text to separate it from the previous line.

---

### 🟦 Difficulty Options:

```python
print("1. Easy (10 chances)")
print("2. Medium (5 chances)")
print("3. Hard (3 chances)")
```

✅ **What it does:**  
These lines describe the three available **difficulty levels**:

- **Easy:** More guesses (10 chances)
- **Medium:** Moderate challenge (5 chances)
- **Hard:** High challenge (3 chances)

🎮 **Game Design Insight:**  
This is a smart way to let players choose how hard they want the game to be — great for all skill levels!

---

## 📝 Summary:

> The `display_welcome()` function is used to show an inviting welcome message, explain the game, and present the difficulty levels to the player. It includes colorful formatting, emojis, and clear instructions to make the game feel friendly and easy to understand. Wrapping this message in a function keeps the code tidy and reusable.

---

## **Step 3: Get Player’s Difficulty Choice**

This piece of code handles **user input validation**, ensuring the player chooses a valid difficulty level. Let’s break it down clearly, line by line, for your audience.

---

## 🔹 Code Snippet

```python
def get_difficulty():
    while True:  # Keep asking until valid input
        try:
            choice = int(input("\nEnter your choice (1-3): "))
            if 1 <= choice <= 3:
                return choice
            else:
                print("Please enter a number between 1 and 3.")
        except ValueError:  # Handles non-integer inputs
            print("Invalid input. Please enter a number.")
```

---

## 🔍 Line-by-Line Explanation


 🟦 `def get_difficulty():`

✅ **What it does:**  
Defines a function named `get_difficulty`. This function is responsible for asking the user to choose a difficulty level and making sure the input is valid.

💡 **Why it's useful:**  
Encapsulating input logic in a function keeps your code clean and makes it reusable.

---

 🟦 `while True: # Keep asking until valid input`

✅ **What it does:**  
Starts an **infinite loop**. The code inside this loop will repeat **forever** unless it’s explicitly stopped using a `return` or `break`.

💡 **Why it's used:**  
You want to keep asking the player for their choice **until** they enter a valid number between 1 and 3.

---

🟦 `try:`

✅ **What it does:**  
Begins a `try` block — Python’s way of saying “try this code, and if something goes wrong, I’ll handle it.”

💡 **Why it's used:**  
To catch and handle invalid user input, like if the user types "hello" instead of a number.

---

🟦 `choice = int(input("\nEnter your choice (1-3): "))`

✅ **What it does:**

- Prompts the user to enter their choice using `input()`.
- Wraps that input with `int()` to convert it into a number.

🧠 **Important Detail:**  
If the user types a **non-number** (like `"two"` or `"abc"`), this line will raise a `ValueError`, which will be caught by the `except` block.

---

 🟦 `if 1 <= choice <= 3:`

✅ **What it does:**  
Checks if the number the user entered is between 1 and 3. This ensures only valid difficulty choices are accepted.

🔒 **Why it's necessary:**  
Even if the user enters a number like 5 or 0 (which doesn’t raise an error), it’s still **not valid** — so this line filters such inputs.

---

 🟦 `return choice`

✅ **What it does:**  
Ends the loop and returns the valid difficulty level to the part of the program that called `get_difficulty()`.

💡 **Why it works here:**  
This line only runs **if** the input passes the checks. Otherwise, the loop continues.

---

🟦 `else: print("Please enter a number between 1 and 3.")`

✅ **What it does:**  
Handles numbers that are technically valid integers but are **outside the allowed range** (like 4, 10, or -1).

---

🟦 `except ValueError:`

✅ **What it does:**  
Catches any **conversion error** caused by trying to convert non-numeric input (like `"hello"`) into an integer.

---

🟦 `print("Invalid input. Please enter a number.")`

✅ **What it does:**  
Gives helpful feedback if the player types something that isn't a number. This makes your app more **user-friendly** and prevents it from crashing.

---

## 📝 Summary for Your Blog Post

> The `get_difficulty()` function ensures players enter a valid difficulty level (1, 2, or 3). It keeps asking until the input is both a **number** and **within the correct range**. This is achieved using a loop, exception handling (`try-except`), and clear user messages. These techniques are essential for building smooth, crash-proof CLI programs.

---

## **Step 4: Get the Player’s Guess**

This block of code is all about ensuring the **player enters a valid number guess** during the game. Let’s break it down clearly, line by line.

---

#### 🔹 Code Snippet

```python
def get_guess():
    while True:
        try:
            guess = int(input("\nEnter your guess: "))
            if 1 <= guess <= 100:
                return guess
            else:
                print("Please enter a number between 1 and 100.")
        except ValueError:
            print("Invalid input. Please enter a number.")
```

---

#### 🔍 Line-by-Line Explanation

---

 🟦 `def get_guess():`

✅ **What it does:**  
Defines a function called `get_guess`. This function will:

- Ask the player for their guess
- Validate it
- Return it if it’s a valid number between 1 and 100

💡 **Why it's useful:**  
It keeps the input-checking logic **separate and reusable**, which makes the code cleaner and easier to manage.

---

 🟦 `while True:`

✅ **What it does:**  
Starts an **infinite loop**. The loop will keep running until the player enters a valid guess.

💡 **Why it's needed:**  
You don’t want the program to continue until the user gives a correct input. This forces them to try again if they mess up.

---

 🟦 `try:`

✅ **What it does:**  
Begins a `try` block — we’ll try to convert the input to an integer. If the user types something invalid (like `"hello"`), Python will raise an error, and we’ll catch it.

---

 🟦 `guess = int(input("\nEnter your guess: "))`

✅ **What it does:**

- Displays the message: `"Enter your guess:"`
- Takes the user's input
- Converts it into an `int` (integer) using `int()`

🧠 **What could go wrong here?**  
If the user types in something that isn’t a number (like "ten"), the `int()` conversion fails — that’s why we use the `try` and `except`.

📘 **UI Tip:**  
The `\n` at the start of the string adds a line break before the prompt — it gives breathing room on the terminal for better UX.

---

 🟦 `if 1 <= guess <= 100:`

✅ **What it does:**  
Checks whether the user's input is within the allowed range — between **1 and 100**, inclusive.

💡 **Why it’s needed:**  
Even if the user enters a number, it still might be **invalid for the game**. For example, `0`, `101`, or `9999` are still numbers — just not allowed here.

---

 🟦 `return guess`

✅ **What it does:**  
If the guess is valid, this immediately ends the function and returns the number so it can be used by the main game logic.

💡 **Why this ends the loop:**  
Since `return` exits the function entirely, the infinite loop ends right here when we get valid input.

---

 🟦 `else: print("Please enter a number between 1 and 100.")`

✅ **What it does:**  
If the number entered is **outside** the valid range, this line lets the user know and prompts them to try again.

📘 **UX Tip:**  
Good error messages are key in user-friendly software — they guide users, not punish them.

---

 🟦 `except ValueError:`

✅ **What it does:**  
If the conversion to integer fails (because the user typed in letters or symbols), this block handles the error gracefully.

---

 🟦 `print("Invalid input. Please enter a number.")`

✅ **What it does:**  
Gives clear feedback if the user enters non-numeric input. Instead of the app crashing with a Python error, the user gets a friendly message.

🧠 **Why this matters:**  
Robust input handling is crucial in making programs that **don’t break easily** and feel professional.

---

## 📝 Summary for Your Blog Post

> The `get_guess()` function handles one of the most important user interactions: getting a valid guess from the player. It uses a **loop** to keep asking until the input is both a number and within the range of 1 to 100. The use of `try-except` ensures the program doesn’t crash when given invalid input. This is a great example of writing user-friendly and bug-resistant code.

---
## **Step 5: The Core Game Logic**

This is the **core logic** of your number guessing game — where all the action happens. For your audience, it’s a great opportunity to show how different programming concepts come together: dictionaries, loops, conditionals, time tracking, user input, and logic branching.

Let’s break it down **line by line** for clarity and comprehension.

---

#### 🔹 Code Snippet

```python
def play_game(difficulty, high_scores):
```

✅ **Defines the main function** that runs the guessing game.

- Takes two arguments:
    - `difficulty`: the level chosen by the player (1, 2, or 3)
    - `high_scores`: a dictionary storing the best performance for each difficulty


---

#### 🔹 Difficulty Setup

```python
    difficulty_settings = {1: 10, 2: 5, 3: 3}
```

✅ A dictionary that maps difficulty levels to the number of allowed attempts:

- Easy → 10 chances
- Medium → 5 chances
- Hard → 3 chances 

---

```python
    max_attempts = difficulty_settings[difficulty]
```

✅ Uses the selected difficulty (1, 2, or 3) to get the correct number of attempts from the dictionary.

📘 **Teaching Tip:** This is a clean way to avoid lots of `if`/`else` conditions.

---

```python
    difficulty_names = {1: "Easy", 2: "Medium", 3: "Hard"}
```

✅ Another dictionary used to convert numeric difficulty level into a readable name.

---

```python
    print(f"\nGreat! You have selected {difficulty_names[difficulty]} difficulty.")
    print(f"You have {max_attempts} chances to guess the correct number. ☺️")
```

✅ Displays a message confirming the selected difficulty and how many attempts the player gets.

💡 Uses **f-strings** to insert values into the text dynamically. F-strings are an easy way to make output more readable and personalized.

---

#### 🔹 Game Initialization

```python
    secret_number = random.randint(1, 100)
```

✅ Generates a random secret number between 1 and 100 that the player will try to guess.

📘 **Blog Tip:** Explain that `random.randint(a, b)` includes both endpoints — so the number could be 1, 100, or anything in between.

---

```python
    attempts = 0
    start_time = time.time()  # Start timer
```

✅ Initializes:

- `attempts`: counts how many guesses the player has made
- `start_time`: records the current time (used later to calculate how long the player took to guess correctly)

---

#### 🔹 Main Game Loop

```python
    while attempts < max_attempts:
```

✅ Runs the guessing loop until the player runs out of attempts.

---

```python
        attempts += 1
        guess = get_guess()
```

✅ Increments the attempt count and then asks the player to input a guess using your previously defined `get_guess()` function.

---

#### 🔹 If Guess is Correct

```python
        if guess == secret_number:
```

✅ Checks if the player guessed the number correctly.

---

```python
            end_time = time.time()
            time_taken = round(end_time - start_time, 2)
```

✅ Records the time taken to win and rounds it to 2 decimal places.

---

```python
            print(f"\n🎉 Congratulations! You guessed the correct number {attempts} attempts. 🎉")
            print(f"⏱️ Time taken: {time_taken} seconds")
```

✅ Displays a success message along with the number of attempts used and time taken.

---

#### 🔹 High Score Update

```python
            if high_scores[difficulty] == 0 or attempts < high_scores[difficulty]:
                high_scores[difficulty] = attempts
                print("🏆 New high score for this difficulty level! 🏆")
```

✅ Updates the high score for the selected difficulty **if:**

- No previous score exists (`== 0`), or
- The player did better than the previous record

📘 **Tip:** This encourages replayability by rewarding better performance.

---

```python
            return True  # Player won
```

✅ Ends the function and returns `True` to indicate the player has won.

---

#### 🔹 If Guess is Wrong

```python
        if attempts < max_attempts:
```

✅ If the player still has attempts left, give them a hint.

---

```python
            if guess < secret_number:
                print(f"Incorrect! ⬆️  The number is greater than {guess}.")
            else:
                print(f"Incorrect! ⬇️  The number is less than {guess}.")
```

✅ Provides directional hints:

- Guess is too low → say it’s greater
- Guess is too high → say it’s lower

This helps guide the player toward the correct answer.

---

```python
            print(f"Remaining attempts: {max_attempts - attempts}")
```

✅ Lets the player know how many chances they have left.

---

#### 🔹 Game Over

```python
    print(f"\n💀 Game over! The number was {secret_number}. 💀")
    return False  # Player lost
```

✅ If the loop ends and the number wasn’t guessed:

- Show a “Game Over” message
- Reveal the correct number
- Return `False` to indicate the player lost

---

## 📝 Summary

 The `play_game()` function is the main engine of the game. It handles:

 - Setting up the number of attempts based on difficulty
 - Generating the secret number   
 - Running a loop where the player guesses 
 - Providing hints and feedback
 - Timing how long it took to win 
 - Tracking high scores 

 It uses powerful concepts like dictionaries, functions, loops, conditionals, and user input to build a fun and interactive experience.

---

## **Step 6: The Main Game Loop**

This final block is the **`main()` function**, the “hub” where everything comes together — input, display, and gameplay logic.

Let’s now walk through this bit **line by line** so your audience understands not just _what_ each line does, but _why_ it's necessary.

---

#### 🔹 Code Snippet

```python
def main():
```

✅ **Defines the main entry point** of the program.

💡 **Why it’s important:**  
This is the function that ties everything together. It sets up the game environment and runs the game loop.

---

#### 🔹 High Score Setup

```python
    high_scores = defaultdict(int)  # Tracks best attempts per difficulty
```

✅ Creates a dictionary-like object called `high_scores` using `defaultdict`.

- `defaultdict(int)` automatically assigns a default value of `0` for any new difficulty level (key).
- This prevents `KeyError` if the player hasn’t played a difficulty before.

📘 **Tip:** This is a safer and cleaner alternative to using a regular `dict`.

---

#### 🔹 Game Loop

```python
    while True:  # Game loop
```

✅ Starts an **infinite loop** to keep the game running until the user chooses to quit.

💡 **Why:**  
It allows the player to replay the game multiple times without restarting the program.

---

#### 🔹 Show Welcome Message & Scores

```python
        display_welcome()
```

✅ Calls the `display_welcome()` function to show the welcome text and difficulty options.

```python
        print("\n🏅 Current High Scores:")
        print(f"Easy: {high_scores[1] or 'No score yet'}")
        print(f"Medium: {high_scores[2] or 'No score yet'}")
        print(f"Hard: {high_scores[3] or 'No score yet'}")
```

✅ Displays the current high scores.

- If no score exists for a difficulty (i.e., score is 0), it shows `"No score yet"`.
- The `or` keyword is a Python shortcut that returns the first truthy value.

📘 **Tip:** Point out how `f-strings` and conditional expressions improve readability and make output dynamic.

---

#### 🔹 Get Difficulty & Start Game

```python
        difficulty = get_difficulty()
```

✅ Asks the player to select a difficulty level using the previously defined `get_difficulty()` function.

```python
        play_game(difficulty, high_scores)
```

✅ Starts the game by calling `play_game()` and passes:

- The chosen difficulty level
- The `high_scores` dictionary so it can be updated if a new record is set

---

#### 🔹 Ask to Play Again

```python
        play_again = input("\nPlay again? (yes/no): ").lower()
```

✅ Asks the user if they want to play again and converts the input to lowercase to handle "Yes", "YES", "yes", etc.

---

```python
        if play_again not in ['y', 'yes']:
            print("\nThanks for playing! 👋")
            break
```

✅ If the answer isn’t “yes” or “y”, the game:

- Prints a goodbye message
- Exits the loop using `break`, which ends the program

📘 **Tip:** Let readers know that this is a common pattern for looping until the user says "no".

---

## 📝 Blog Summary

The `main()` function is the **central controller** of the number guessing game. It:

- Initializes the high scores
- Displays the welcome message and scores
- Loops through the game until the player chooses to stop
- Handles input and feedback for difficulty and replay

It also demonstrates best practices like:
- Using `defaultdict` to safely manage dynamic data
- Using loops and functions to create a seamless player experience
- Cleanly breaking out of loops when needed


---

## **Step 7: Run the Game**

At the very end of your script, you'd typically add:

```python
if __name__ == "__main__":
    main()
```

This ensures the `main()` function runs **only when the script is executed directly**, not when it's imported as a module.


---

