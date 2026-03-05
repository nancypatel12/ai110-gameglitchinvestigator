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
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: _"How do I keep a variable from resetting in Streamlit when I click a button?"_
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

- [x] Describe the game's purpose.
  - A number guessing game built with Streamlit where the player tries to guess a secret number within a limited number of attempts. The game has difficulty settings (Easy, Normal, Hard) that change the range and attempt limit, a scoring system, and a hint system that tells you whether to go higher or lower.

- [x] Detail which bugs you found.
  1. **Reversed hints** (`app.py`, `check_guess` function): When the guess was too high, the hint said "Go HIGHER!" and when it was too low, the hint said "Go LOWER!" -- the exact opposite of what they should say.
  2. **New Game button broken** (`app.py`, `new_game` handler): Clicking "New Game" never reset `session_state.status` back to `"playing"`, so after winning or losing, the game stayed stuck on the end screen. It also didn't clear `history` or `score`, and used a hardcoded range instead of the selected difficulty range.
  3. **Show Hint toggle didn't persist** (`app.py`, hint display logic): The hint message was only rendered inside the `if submit:` block, so toggling the "Show hint" checkbox off and back on would lose the hint. The hint was never stored in session state.
  4. **Difficulty ranges swapped** (`app.py`, `get_range_for_difficulty`): Normal was 1-100 and Hard was 1-50, which is backwards. Normal should be 1-50 and Hard should be 1-100.
  5. **Changing difficulty didn't affect the game** (`app.py`): Switching difficulty in the sidebar didn't reset the secret number or the game state, so the game always played with the original range. The info text was also hardcoded to say "between 1 and 100" regardless of difficulty.
  6. **No validation for out-of-range guesses** (`app.py`, `parse_guess`): The game accepted any number, even ones outside the valid range, wasting attempts on impossible guesses.

- [x] Explain what fixes you applied.
  1. **Fixed reversed hints**: Swapped the hint messages in `check_guess` so "Too High" returns "Go LOWER!" and "Too Low" returns "Go HIGHER!" -- fixed in both the normal path and the TypeError fallback.
  2. **Fixed New Game button**: Added `st.session_state.status = "playing"` to the reset logic, cleared `history` and `score`, set `attempts` to `1` (matching initial state), and used `random.randint(low, high)` to respect the current difficulty.
  3. **Fixed Show Hint toggle**: Added `last_hint` to session state, stored the hint message on each guess, and moved the hint display outside the submit block so it renders whenever the checkbox is checked.
  4. **Fixed difficulty ranges**: Swapped Normal to 1-50 and Hard to 1-100 so the difficulty actually scales correctly (Easy: 1-20, Normal: 1-50, Hard: 1-100).
  5. **Fixed difficulty switching**: Added difficulty tracking in session state so changing the setting resets the game with the correct range. Updated the info text to show the actual range instead of hardcoded "1 and 100".
  6. **Added out-of-range validation**: `parse_guess` now takes `low` and `high` parameters and rejects guesses outside the valid range with an error message.

## 📸 Demo

- [ ] [Insert a screenshot of your fixed, winning game here]

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, insert a screenshot of your Enhanced Game UI here]
