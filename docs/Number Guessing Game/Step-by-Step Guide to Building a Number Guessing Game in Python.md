
In this blog post, we'll walk through building a **Number Guessing Game** in Python. This game will allow players to guess a randomly generated number while tracking high scores across different difficulty levels.  

By the end of this guide, you'll understand:
- How to generate random numbers
- How to implement difficulty levels
- How to track high scores
- How to validate user input
- How to structure a Python game loop

---

## **Step 1: Import Required Modules**

We start by importing the necessary Python modules:
```python
import random  # For generating random numbers
import time    # For tracking game duration
from collections import defaultdict  # For storing high scores
```

### **Why These Modules?**

- `random` → Generates the secret number.
- `time` → Measures how long the player takes.
- `defaultdict` → Efficiently stores high scores for different difficulties.

---

## **Step 2: Display the Welcome Screen**

This function introduces the game and explains difficulty levels:
```python
def display_welcome():
    print("\033[1;36m\nWelcome to the Number Guessing Game!\033[0m")
    print("I'm thinking of a number between 1 and 100. 🧠")
    print("\nDifficulty levels:")
    print("1. Easy (10 chances)")
    print("2. Medium (5 chances)")
    print("3. Hard (3 chances)")
```

### **Why This Function?**

- Gives players clear instructions.
- Explains available difficulty levels.

---

## **Step 3: Get Player’s Difficulty Choice**

This function ensures the player selects a valid difficulty (1-3):
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

### **Why This Function?**

- Uses `try-except` to prevent crashes from invalid inputs.
- Ensures the player picks a valid difficulty.

---

## **Step 4: Get the Player’s Guess**

This function ensures the player enters a valid guess (1-100):
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

### **Why This Function?**

- Prevents invalid guesses (like words or out-of-range numbers).
- Keeps the game running smoothly.

---

## **Step 5: The Core Game Logic**

This function runs the actual game:

```python
def play_game(difficulty, high_scores):
    # Difficulty settings (attempts)
    difficulty_settings = {1: 10, 2: 5, 3: 3}
    max_attempts = difficulty_settings[difficulty]
    difficulty_names = {1: "Easy", 2: "Medium", 3: "Hard"}
    
	# Game setup
    print(f"\nGreat! You have selected {difficulty_names[difficulty]} difficulty.")
    print(f"You have {max_attempts} chances to guess the correct number. ☺️")
    
    secret_number = random.randint(1, 100)
    attempts = 0
    start_time = time.time()  # Start timer
    
	# Main game loop
    while attempts < max_attempts:
        attempts += 1
        guess = get_guess()
        
        if guess == secret_number:
            end_time = time.time()
            time_taken = round(end_time - start_time, 2)
            print(f"\n🎉 Congratulations! You guessed the correct number {attempts} attempts. 🎉")
            print(f"⏱️ Time taken: {time_taken} seconds")
            
            # Update high score if better than previous
            if high_scores[difficulty] == 0 or attempts < high_scores[difficulty]:
                high_scores[difficulty] = attempts
                print("🏆 New high score for this difficulty level! 🏆")
            return True  # Player won
        
        # Give hints if wrong
        if attempts < max_attempts:
            if guess < secret_number:
                print(f"Incorrect! ⬆️  The number is greater than {guess}.")
            else:
                print(f"Incorrect! ⬇️  The number is less than {guess}.")
            print(f"Remaining attempts: {max_attempts - attempts}")
    
    print(f"\n💀 Game over! The number was {secret_number}. 💀")
    return False  # Player lost
```

### **Why This Function?**

- Uses `random.randint()` to pick a secret number.
- Tracks attempts and time.
- Updates high scores if the player does better than before.
- Gives feedback ("Higher/Lower") to guide the player.

---

## **Step 6: The Main Game Loop**

This function ties everything together:

```python
def main():
    high_scores = defaultdict(int)  # Tracks best attempts per difficulty
    
    while True:  # Game loop
        display_welcome()
        print("\n🏅 Current High Scores:")
        print(f"Easy: {high_scores[1] or 'No score yet'}")
        print(f"Medium: {high_scores[2] or 'No score yet'}")
        print(f"Hard: {high_scores[3] or 'No score yet'}")
        
        difficulty = get_difficulty()
        play_game(difficulty, high_scores)
        
        play_again = input("\nPlay again? (yes/no): ").lower()
        if play_again not in ['y', 'yes']:
            print("\nThanks for playing! 👋")
            break
```

### **Why This Function?**

- Uses `defaultdict` to store high scores.
- Shows current high scores before each game.
- Allows replaying without restarting the program.

---

## **Step 7: Run the Game**

Finally, we start the game when the script runs:
```python
if __name__ == "__main__":
    main()
```

### **Why This Check?**

- Ensures the game only runs when executed directly (not when imported as a module).

---

## **Final Thoughts**

This game demonstrates:
✅ **Random number generation** (`random.randint`)  
✅ **User input validation** (`try-except` blocks)  
✅ **Difficulty levels** (adjusting attempts)  
✅ **High score tracking** (`defaultdict`)  
✅ **Game loop structure** (play again option)  

### **Possible Extensions**

- Add a **leaderboard** to save scores between sessions.
- Implement a **timer mode** where players must guess quickly.
- Add **sound effects** for correct/incorrect guesses.

---

