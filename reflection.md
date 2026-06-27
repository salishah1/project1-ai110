# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").


  When I first ran it, I noticed right away that despite it having specific measures for each difficulty level, the banner looked about the same. No matter the range, the instructional banner was hardcoded to give the range 1 - 100 (1) and the attempts left count was 1 less than the actual attempts we have(2). 

  The proper information of the range, and attmepts left was on the left side section where difficulty levels could be changed. 

  The attempts count is off; before you begin guessing, it labels attmepts as 1; then for exmaple you guess 3 numbers, it only logs 2 of them but counts it as 3 attmepts. 

  It is not counting the last attempt, it is just 1 higher than what is logged due to starting at 1. 

**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
1. When the first attmept is submitted, it is not logged; when the next guess is submitted, the previous attempt is logged.
2. If the guess is 1+ less than the actual number, the hint will say go lower; if the guess is 1+ higher than the actual number, it will say to go higher
3. I also noted that when the game was over, regardless of whether you won or lost, it would not let you restart; attempt restarts at 1, and the secret number is reset but it doesn't refresh the history, nor does it let you guess a number for the new game. 
4. If you have not guess the correct number by the amount of given attempts (in the instructional banner, so 1 - the actual attempt allowance), you will lose the game


Game Log - played many games, here are the most interesting ones: 

Difficulty: Normal; Attempts: 8; Range: 1 - 100; Secret Number: 87

|30 (Attempt 1/8)|Expected: go higher; logs 30 |Actual: Go lower; 30 is not logged
|45| Expected: Go higher; logs 45|Actual: Go lower; logs 30; attmept count: 2
|90| expected: Go lower; logs 90 |Actual: Go Higher; logs 45; score: -10
...
|88|Expected: Go lower; logs 88|Actual: Go Higher; logs 85
|87 (Attempt 7/8)| Expected: Logs 87; correct and win message |Actual: logs 88, correct!; win message; final score: -10; attempt count set to 0

|Says You won, start new game to try again, but can't guess for new game|


Game 2:
Difficulty: Normal; Attempts: 8; Range: 1 - 100; Secret Number: 86
- said I had 7 attempts; did only 6 attempts; it logged 5, but counted 6 

...
attempt 6: (banner says 1 attempt left; but ends game on attempt 6 instead of attmept 7; even though there was to be 8 attempts)

|Guess: 80|Expected: Log 80; go higher|Actual: Go lower; logs 2|Console Output: Out of attempts, secret number was 86 (even though banner says 1 attempt still left); Final score: -35; (never logs 80, my last guess)


Game 3: 
Difficulty: Easy; Attempts: 6; Range: 1-20; Secret Number: 76
- banner says 5 attempts only

Attempt 5: (ends game on 5 attempts instead of 6)

Guess: 73|Expected: Logs 73; go higher|Actual: Logs 97; Go lower|Console: Out of attempts, start new game to try again; secret number was 76. Final Score: -15


Game 4: 
Difficulty: Hard; Range: 1- 50; Attempts: 5; Secret Number: 66

4 guesses ...

Attempt 5. Guess: 65|Expected: Go higher; logs 65|Actual: Go lower; logs 2| I still have more guesses

Attempt 6. Guess: 66|expected: Correct; Logs 66|Actual: Correct!; Logs 65, does not ever log 66; Randomly gives me 6 attempts|
Console output: You won; secret number: 66; Final score: 5
Start new game to try again (does not let me play new game)

Game 5: 
hard level; secret number: 38; 5 attempts, 

counts it as 4 attempts left in the banner 

Upon my 4th guess (and logging only the first 3 guesses) it gives me the regular error message: 

"Out of attmepts; You lost; Secret number was 38; Final score: -5
Start new game to try again"



---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)?

I used Claude Code and Claude Chat

- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).

It correctly found that I couldn't play a new game because when a new game starts, the session status is not reset along with the attempts count and the secret number. 

It gave me all the line numbers and I looked at the code directly to verify this. Also, that was clear from when I was playing the game. 



- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

I had an issue with the way it was counting the attempts made because it was affecting the games. It suggested fixing the new game issue and letting the attempts for every new game click start at 0 and leave the rest starting at 1. But this only creates more of an issue, and doesn't fully solve the attempts count problem. 

Furthermore, it kept suggesting, despite my corrections and the game logs I gave it to reference in the reflections file that: 
in a new game, attempts is initialized at 1 (which is true), then when I put in my first guess, it increments to 2, therefore the cutting off my game due to attempts wrongfully running out is fair. 

Both of these were things I could reject due to experience, common sense, and my game logs. 

In my game logs(in the file and those I did not mark in the file), it clearly states that upon the first submisson, the attmepts count starts and stays at 1 (as is) and only on the second submission is the first submission logged and attempts is incremented to account for the first (logged) submit. 

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
  I tested the game both manually and through pytest just to double check. I made chnages that were necessary and reran the tests until they worked.


- Describe at least one test you ran (manual or using pytest)  

I ran python -m pytest tests/ -v. The three tests in test_game_logic.py check check_guess: (50, 50) should return "Win", (60, 50) → "Too High", and (40, 50) → "Too Low". At first all three failed with NotImplementedError because logic_utils.py was still stubbed, and even once I copied my logic over they would have failed because my app's check_guess returned a tuple ("Win", message) instead of the plain string the tests expect. Making check_guess return just the outcome string (and moving the arrow text into a separate hint_message helper) turned it into 3 passed — which confirmed both that my higher/lower comparison was correct and that the function's return shape matched what the rest of the code relied on.

  and what it showed you about your code.
- Did AI help you design or understand any tests? How?

The tests were given to me, so AI didn't write them, but it helped me understand why they were failing. It pointed out the return-type mismatch — that the test asserts a string while my code returned a tuple.  It also caught a stray from curses import raw import that would have made every test error out on Windows before any assertion even ran.

---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?

Every time you interact with a Streamlit page — click a button, type in a box, change a dropdown — Streamlit re-runs your entire Python script from the top, line by line. That means ordinary variables don't remember anything; they get rebuilt from scratch on every interaction. To keep things between runs — the secret number, the score, how many attempts you've used — you store them in a special container, st.session_state, which Streamlit keeps alive across reruns. This project taught me two sides of that: because the script runs top-to-bottom, where you write code matters — my banner and debug panel were written above the code that updated the attempt count, so they showed the previous run's numbers until I redrew them after the guess was processed. And session state persists so reliably that the New Game button looked broken — the finished game's status was still in session_state, so I had to explicitly reset it (plus history and score) for a new game to actually start.

---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?

  - This could be a testing habit, a prompting strategy, or a way you used Git.
- 
Keeping a concrete reproduction log — the exact inputs, what I expected, and what actually happened. I wrote down real game runs (difficulty, secret, each guess, the attempts count, the hint shown), and that log was what let me confirm a bug was truly fixed and, just as importantly, push back when an explanation didn't match reality. I want to keep logging concrete cases like that instead of relying on "it seems fixed.

- What is one thing you would do differently next time you work with AI on a coding task?

I gave the AI my game logs and context up front and used its line numbers and traces to verify things, but it still kept confidently asserting wrong claims about how the game behaved and about my own experience — and I corrected each one right away with my logs. So the lesson isn't that I needed to react faster or hand over more context; I'd already done both. What I'd do differently is to ask it to demonstrate a behavioral claim (by actually running or tracing the code) before I believe it. I do also want to develop better promopting stategies.


- In one or two sentences, describe how this project changed the way you think about AI generated code.

The whole broken game was AI-generated and even called itself "production-ready," yet it was full of subtle, interacting bugs. It taught me that AI code can look polished and confident while being quietly wrong, so I have to read it, test it, and verify behavior myself rather than trust it.