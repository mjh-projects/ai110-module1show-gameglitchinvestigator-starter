# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

- What did the game look like the first time you ran it?
- List at least two concrete bugs you noticed at the start  
  (for example: "the hints were backwards").
  Hints were backward
  Out of range numbers accepted
  Pressing new game did not start new game

**Bug Reproduction Log**

Document at least 3 bugs you found. Add rows as needed.
Hints were backwards
It allowed entries beyond bounds like negative or over 100
It did not start a new game

| Input      |   Expected Behavior | Actual Behavior | Console Output / Error |
|-------        |-------------------|-----------------|------------------------|
| number<secret |Hint: Go Higher    | Go Lower        | incorrect hint         |
| number>secret |Hint: Go Lower     | Go Higher       | incorrect hint         |
|Out of range eg. 0 , 101    | error out of range| incorrect hint  | was not flagged as an error|
|New Game    |reset & start new  | game was not    | same game remained 
                 game                 reset
---

## 2. How did you use AI as a teammate?

- Which AI tools did you use on this project (for example: ChatGPT, Gemini, Copilot)? Claude code
- Give one example of an AI suggestion that was correct (including what the AI suggested and how you verified the result).
On every even-numbered attempt, the secret gets converted to a string before being passed to check_guess. That breaks the comparison:

In check_guess, guess > secret compares an int to a str, which raises TypeError.
The except TypeError fallback then does g = str(guess) and compares the two values as strings (lexicographic), not as numbers. So e.g. "9" > "85" is True, telling you to "Go LOWER" when 9 is actually lower than 85. The hints come out wrong/inconsistent.
Fix
Always pass the numeric secret. 

The root cause: the handler reset only attempts and secret, but left status at "won" or "lost". After the rerun, this guard fired immediately:


if st.session_state.status != "playing":
    ... st.stop()
…so the page stopped with "You already won / Game over" and never became playable. The fix resets all game state — status="playing", score, history, attempts, and a fresh secret drawn from the current difficulty range (it was hardcoded to 1-100 before, ignoring Easy/Hard)

- Give one example of an AI suggestion that was incorrect or misleading (including what the AI suggested and how you verified the result).

Both seemed correctx

---

## 3. Debugging and testing your fixes

- How did you decide whether a bug was really fixed?
- Describe at least one test you ran (manual or using pytest)  
  and what it showed you about your code.
- Did AI help you design or understand any tests? How?
I ran the game manually again to verify the hints were correct and if I could start a new game. 
I also ran the unit tests that the AI composed in the test_game_logic.py


---

## 4. What did you learn about Streamlit and state?

- How would you explain Streamlit "reruns" and session state to a friend who has never used Streamlit?
 Streamlit re-runs your whole script on every click, so regular variables forget everything instantly — session_state is the dedicated memory box for the few things you need to survive between those re-runs.



---

## 5. Looking ahead: your developer habits

- What is one habit or strategy from this project that you want to reuse in future labs or projects?
 
  - This could be a testing habit, a prompting strategy, or a way you used Git.
- What is one thing you would do differently next time you work with AI on a coding task?
- In one or two sentences, describe how this project changed the way you think about AI generated code.
I want to break down the project into understandable sections so I am sure to keep track of what AI is changing and keep track of code and understand it.



I see that AI can be helpful in generating code while still allowing me to learn and understand what is going on. 