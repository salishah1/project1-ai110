# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

- [ ] Describe the game's purpose.
AI created a game based on prompting instructions where it holds a secret number from a particular rnage and we have a set number of guesses to make to guess that secret number. 

The game had lots of errors and glitches, that not only took away from the correct application of every feature but also made the actual game harder/ at times impossible to play. 

- [ ] Detail which bugs you found.
I found many errors in AI's implementation: 
1. Hints were backwards - going 1+ more than the secret number would produce the go higher hint and going 1+ less than the number would produce the go lower hint. 
- [ ] Explain what fixes you applied.
1. Issue: I noticed that (lines 158 - 161) the secret number is convrted into a string meaning the comparison was done not as int to int but char to char; also the hint messages were swapped(lines 38 & 40)
Fix: removong the str conversion of the secret number; comparing using <, >, == so it's comparing the whole int; and swapping the hint messages to where they should be. 

2. Instructional banner was wrong: the range there always said 1 - 100 regardless of the range specificed with the difficulty level and the attempts left count was one off
Issue: 1 - 100 was hardcoded in the banner; attempts was initialized wrong, and left was attempts - used. 
Fix: have the banner be written using the high and low properties of every diffciulty level; empty for initialization, then rewritten at every run, so the attempts left is computed correctly. 

3. Attempts count was wrong: Banner was one off, counter starts at 1, couter upodates slowly and weirdly, not getting full attempts
Issue: Attempts is initialized with a 1 instead of 0 leading to all these errors, ony resets to 0 for a new game(which we cant play)
Fix: Initialize attempts with 0; ensure attempts is incremented after the input is validated. 

4. Secret number was also out of range; even on changing settings it would not reflect the range of the new setting, nor would be regenerated as needed (only regnerated when you clicked refresh/ clicked new game button)
Issue: Secret was following the hardcoded 1 - 100 range, and was only resetting during new game clicks
Fix: have secret also follow the high and low properties of each range setting; also added a setting change detector that reflected any chnages made immediately


5. Guesses were not logging appropriately affecting the attempts count too; submit 50, nothing logged; submit 42, 50 logged, attempts incremented
Issue: File runs top to bottom and the guess logging was too far down so the previous state is what runs and then the guess is appended
Fix: Starting the banner and debug panel at empty and then redraw it after the full processing, ensuring that validation happens before any changes are made, then the increments and redraws work.

6. New game didnt let user play- after a game ends, you are stuck unable to start a new game 
Issue: On the surface, the secret number, and attempts was reset and the difficulty setting chnages were reflected but no guess could be made. History, score and status were not reset
Fix: Fix status to reset to playing; ensure histry and score are also reset for the new game; make sure the secret number is regnerated in accordance to the high and low range of the difficulty setting (and any changes are immediatley reflected)

7. Refactor fix: the tests needed a string but the check guess original function returned a tuple so the test still fails
Fix: In logicutils.py file, ensure that the method is returning the string of win; arrow is moved to the hint message helper method; app.py reflected these chnages by calling on these functions

Also, the curses import statment was failing the tests and wasnt available in windows so it was removed as well.

## 📸 Demo Walkthrough

Describe your fixed game in numbered steps so a reader can follow along without watching a video:

1. Pick a difficulty.
 In the sidebar, choose Easy, Normal, or Hard. This sets the range you're guessing in and how many tries you get: Easy is 1–20 with 6 attempts, Normal is 1–100 with 8, Hard is 1–50 with 5. The banner up top always shows the current range and how many attempts you have left. 


2. Take your first guess. 
Type a number from the range into the box and click Submit Guess. A natural opening move is the middle of the range — say 50 on Normal — since it splits the possibilities roughly in half. Your guess shows up in the history and your "Attempts left" drops by one.

3. Read the hint and close in.
 After each guess the game tells you "📈 Go HIGHER!" or "📉 Go LOWER!". Use it like you would in real life: if 50 was too low, jump up to around 75; if that's too high, fall back toward the middle of what's left. Every hint shrinks the range, so you can usually corner the number in just a few guesses.

4. Keep an eye on your attempts.
 The banner counts your remaining guesses down to zero. Hitting submit on an empty box or something that isn't a number just shows a reminder — it doesn't waste a turn, so only real guesses count.

5. Win or lose.
Land on the secret number and you get balloons, the reveal, and your final score. Run out of attempts first and the game shows you the secret and ends the round.

6. Play again and shift around with options
Click New Game to reset and start over instantly, or switch the difficulty in the sidebar to jump into a new round. Either way you get a fresh secret number (always inside the range shown) and a full set of attempts.

There are lots of settings and difficulty levels to choose from for a unique experience so go ahead and play around with all your options. 


**Screenshot** *(optional)*: <!-- Insert a screenshot of your fixed, winning game here -->

## 🧪 Test Results

```
$ python -m pytest tests/ -v
============================= test session starts =============================
platform win32 -- Python 3.13.7, pytest-9.0.3, pluggy-1.6.0
rootdir: C:\Users\shuma\Desktop\AI 110\project1-ai110
collected 3 items

tests/test_game_logic.py::test_winning_guess PASSED                      [ 33%]
tests/test_game_logic.py::test_guess_too_high PASSED                     [ 66%]
tests/test_game_logic.py::test_guess_too_low PASSED                      [100%]

============================== 3 passed in 0.05s ==============================
```

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, describe the Enhanced UI changes here — a screenshot is optional]
