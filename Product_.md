# PyBe
## A Cognitive Learning System for Computational Thinking

*VLED Lab, IIT Ropar*

**Mentor:** Dr. Prakash Hegade

### Contributors
- **Product Vision & Research Architecture:** Sneha Rajpoot
- **Feature Design & Gamification:** Muskan Kumari

> Every learner already knows how to think computationally.
> They make decisions. They repeat tasks. They group related things. They name patterns.
> PyBe does not install new thinking.
> It makes existing thinking precise enough for a computer to understand.
> Python is simply the notation we use to achieve that precision.

---

## The Problem

Every existing platform that teaches programming makes the same mistake.

They treat learning as content delivery. Syntax is explained. Examples are shown. Exercises are completed. The learner reproduces what they have seen — and forgets it within weeks.

The deeper problem: these platforms measure what the learner *knows*. None of them measure how the learner *thinks*. They have no model of cognitive progression — only content progression.

The result is a learner who can write a loop but cannot tell you when they need one, or why it exists, or what thinking it replaces.

PyBe is built to fix this at the root.

---

## What PyBe Is

PyBe is not just a Python learning platform.

PyBe is a system that does four things simultaneously:

- **Observes** — reads what the learner writes in natural language and maps how they are thinking
- **Measures** — quantifies the structure of their reasoning and locates them on a cognitive trajectory
- **Challenges** — generates a scenario of exactly the right kind to push their thinking forward
- **Remembers** — tracks every belief, every misconception, every breakthrough, across every session

Python appears at the end of this process — as the precise written form of thinking the learner already did in plain language. Not as the starting point.

---

## The Core Idea — Structuredness as a Computational Parameter

Problems are not just easy or hard. They differ in *how structured they are* — how many valid solutions exist, how clearly the goal is stated, how much information is missing, how many constraints compete.

A beginner does not need simpler problems. They need *more structured* problems — where the path is clear and one answer is correct. As thinking matures, the learner can handle increasing ambiguity, competing constraints, and problems with no single right answer.

This is the axis PyBe uses. Not difficulty. Structuredness.

Every scenario in PyBe is represented as a point in structuredness space, defined by four measurable dimensions:

```
S = f( ambiguity, solution_space, goal_clarity, competing_constraints )

S ∈ [0, 1]
0 → fully structured — one answer, explicit goal, no ambiguity
1 → fully ill-structured — infinite answers, implicit goal, maximal tension
```

The scenario generator does not think:
*"Give this learner a beginner loop problem."*

It thinks:
*"Generate a scenario at S = 0.18 for a learner whose current cognitive boundary sits at S = 0.22."*

This is the difference between a fixed curriculum and an adaptive engine.

Learners do not move through levels. They move through structuredness space along a trajectory computed from their own cognitive state — at their own pace, in their own direction.

---

## The Belief Monitoring System

This is the original research contribution!

Every session, as the learner reasons through a scenario in natural language, the system extracts *belief statements* — explicit or implied claims about how Python works:

- *"I think a variable can only hold one value"* — Session 3
- *"A list holds many things"* — Session 6

These are stored per learner, tagged to concepts, timestamped.

After every session, a contradiction detector scans the full belief graph. In the example above:

> A variable holds one value. A list holds many values. But `x = [1, 2, 3]` is a variable containing a list. The learner's two beliefs, held together, are logically incompatible — and they do not know it.

When a contradiction is detected, PyBe does not correct the learner. It engineers a scenario where the learner *walks into their own contradiction* and must reason their way out. The system only asks questions. The resolution is always the learner's own.

This is what real conceptual change looks like. Not correction from outside — collision from within.

The system also detects *overgeneralization* — when a correctly-learned concept is applied outside its valid boundary:

- `=` learned as assignment → `if x = 5:` written in a condition
- `range()` learned for counting → `range("hello")` attempted
- Functions learned as return-producing → `print()` expected to return its output

Together, contradiction detection and overgeneralization detection form a complete belief monitoring system. No existing learning platform does either.

---

## What PyBe Builds

**Track 1 — The AI Mentor**
Replaces the current rule-based, keyword-matching engine with a real LLM that reads what the learner actually wrote. Generates Python code derived from the learner's specific reasoning — not selected from templates. Every explanation is grounded in foundational Python literature through a RAG pipeline (ChromaDB). Every concept arrives with its origin story: the problem that made it necessary, not just the syntax that defines it.

**Track 2 — Belief Monitoring + Contradiction Engine**
Extracts belief statements from natural language every session. Maintains a persistent belief graph per learner. Detects contradictions across sessions and engineers targeted resolution scenarios. Detects overgeneralization and surfaces boundary violations. The original research contribution of this project.

**Track 3 — Structuredness Engine + Assessment**
Implements the four-dimensional structuredness scoring system with a hybrid pipeline: human-scored calibration corpus as ground truth, LLM scoring validated against it, behavioral signals correcting scores at runtime. The assessment tests thinking — not knowledge. Every test scenario is uniquely generated. Mid-session, one constraint changes without warning. The assessment is designed around the learner's own misconception profile. The output is not a score — it is a thinking fingerprint across five cognitive dimensions.

**Track 4 — Interface + Documentation**
Builds the learner-facing experience: an abstraction ladder showing exactly where the learner sits between concrete experience and Python fluency, a belief evolution dashboard showing how their thinking has changed over time, an episodic memory timeline anchoring every breakthrough to the scenario where it happened. Complete documentation — every feature explained, every design decision justified, every research principle cited.

---

## The Narrative Layer

Scenarios in PyBe are not exercises. They are episodes in an ongoing story the learner inhabits.

The learner does not see:
*"Exercise 4: Write a loop to process a list."*

They see:
*"There are 500 students waiting for their results. Are you really going to check each one by hand?"*

Every scenario is built around a situation the learner genuinely recognizes — a shopkeeper, a bus conductor, a student managing expenses. The concept the learner needs does not exist yet in their vocabulary. They feel the need for it before they know its name.

Progression is described in terms of thinking, not syntax:

| Stage | What the Learner Can Now Do |
|---|---------------------------------|
| 1 | Name things precisely |
| 2 | Repeat actions without repetition |
| 3 | Make decisions from context |
| 4 | Create reusable rules |
| 5 | Choose the right container |
| 6 | Find why something is broken |
| 7 | Design systems with no single right answer |
| 8 | Navigate competing valid goals |

Only after completing a stage does the learner realize: *"I have basically learned variables"* — or loops, or functions, or classes. The concept arrives after the thinking. Never before.

---

## Feature Extensions (Gamification & Engagement)

The following features are proposed to improve learner engagement and long-term retention in PyBe.

> ### Features and Ideation
> The modules specified below—the **"What-If" Simulator**, **Interactive Easter Eggs** and **PySnakes & Ladders**— gamified, conceptualized and as a core retention mechanism to maximize user engagement and retention in the PyBe prototype.

---

##  1. Feature: "What-If" Scenario Simulator

### What is it? (In Simple Words)
Once a user writes a prompt and the system runs the Python code, we don't want them to just read the output and leave. The "What-If" feature gives the user a set of sliders, toggles, or quick buttons to tweak variables in that scenario instantly. 

Without rewriting the prompt or waiting for the AI to generate new code, the user can see how changing one number or condition dramatically alters the Python output in real-time.

### Real-World Example
* **The Core Scenario:** A user prompts the AI to calculate the total salary of an employee after taxes. The AI generates the code and shows: `Take-home Salary: ₹80,000`.
* **The "What-If" Panel:** Right below the console, a few interactive options pop up:
  * *Slider:* "What if Inflation rises to 12%?"
  * *Toggle:* "What if the employee gets a 20% performance bonus?"
  * *Button:* "What if the Government introduces a new 30% luxury tax?"

### How it works for the User:
1. The user drags the **Performance Bonus** toggle to **ON**.
2. Behind the scenes, the system updates the variable `bonus = 0.20` directly inside the already generated Python code.
3. The code instantly re-runs inside the browser console, changing the output to: `Take-home Salary: ₹96,000`.
4. **The Learning:** The user intuitively understands how dynamic variables and mathematical logic operate in Python without getting bogged down by syntax.

---

## 2. Feature: Context-Aware Interactive Easter Eggs

### What is it? (In Simple Words)
Since PyBe uses an open text box for prompts, users will naturally try to test its limits by typing funny, non-serious, or rebellious phrases. Instead of showing a boring "Error: Invalid Input" message, the system traps these specific words and replies with a hidden joke, a funny meme, or a cool UI animation. 

This creates an "Aha!" moment, keeps the app highly engaging, and encourages users to share screenshots on social media (LinkedIn/X), making the prototype viral.

### Curated Easter Egg Examples

| What the User Types (The Prompt) | What Happens on the Screen (The Surprise) |
| :--- | :--- |
| **"hack nasa using python"** | A scary, matrix-style red dialog box flashes: `Accessing Satellite... Wifi Too Slow.` <br><br> *Terminal Message:* "NASA's firewall is too strong for your current browser. Go back and learn loops first before executing space espionage!" |
| **"write my college assignment"** | A popular meme template pops up on the screen. <br><br> *Terminal Message:* "Nice try! PyBe is your AI coding mentor, not your underpaid freelance developer. Let's learn how to write this code yourself, step-by-step." |
| **"make me a coffee"** | A small coffee mug animation appears. <br><br> *Terminal Message:* "Error 418: I am a teapot. Please check your kitchen cabinet for real caffeine fixes." |
| **"is python better than java?"** | *Terminal Message:* "Obviously. Do you really want to write 5 lines of boiler-plate code just to print 'Hello World'? 🐍 > ☕" |

### How it works under the hood:
Before the user's text is sent to the AI engine, a tiny layer of code checks if the prompt matches any of these fun keywords. If it matches, it skips the AI generation entirely and instantly triggers the custom response or animation, keeping the system extremely fast and cost-free.

---

##  3. Feature: PySnakes & Ladders (Gamified Learning Board)

### What is it? (In Simple Words)
This feature turns Python learning into a classic 100-cell Board Game (Snake & Ladders). Instead of moving through boring text chapters, the user rolls a digital dice, moves across the board, and unlocks Python concepts. 

The twist? The **ladders** are "Pythonic Discoveries" (writing clean code), and the **snakes** are notorious "Python Runtime Errors" that bite you and pull you down if you can't debug them!

### The Gameplay Mechanics
1. **Roll the Dice:** The user clicks a "Roll" button. A random number (1-6) moves their avatar forward on a beautiful HTML5/CSS grid board.
2. **Landing on a Neutral Cell:** A pop-up asks a quick Python MCQ (Multiple Choice Question) based on their current level. Answer correctly, Stay there. Answer wrong,Step back 1 cell.
3. **Climbing the Ladders (Pythonic Discoveries):** Landing on a ladder box presents a prompt challenge. If the user writes a great prompt that optimizes code, they climb up!
4. **Getting Bitten by the Snakes (Python Runtime Errors):** Landing on a snake head triggers a critical coding error screen. The user must fix the error to avoid sliding down the snake's tail.

### The Game Elements & Coding Vocabulary

| Board Element | In-Game Trigger Name | What Happens? (The Gameplay Twist) | Learning Outcome |
| :--- | :--- | :--- | :--- |
|  Ladder 1 | `List Comprehension shortcut` | User learns how to write a 1-line loop instead of 4 lines. Short and fast! | **Climbs from Cell 14 to 48.** |
|  Ladder 2 | `Mastering f-Strings` | User discovers clean string formatting instead of messy concatenations. | **Climbs from Cell 55 to 77.** |
| Snake 1 | `IndentationError` | The user's code structure lacks proper spacing. The classic beginner trap! | **Bites and drops from Cell 32 to 10.** |
| Snake 2| `TypeError` | Trying to add a String to an Integer (`"5" + 5`). The data types don't match. | **Bites and drops from Cell 64 to 36.** |
| Snake 3 | `IndexError` | Trying to access the 5th item in a list that only has 3 items. | **Bites and drops from Cell 95 to 52.** |

### Why this works perfectly without a Login Flow:
* **Instant Session Play:** Since it's a 5-10 minute game loop, a user can finish a whole match in one sitting.
* **Auto-Save Progress:** The current cell position, board state, and score are silently stored in the browser's `localStorage`. If the user accidentally refreshes, they resume exactly on the same cell with their dice ready!

---
## Open Questions for Discussion

Three genuine research questions this project will generate data to answer:

1. Can structuredness be reliably measured from natural language scenario descriptions — and does measured structuredness predict learner cognitive load?

2. Does contradiction-targeted scenario generation resolve misconceptions faster than standard instruction — and is that effect stronger at higher structuredness levels?

3. Does progression through structuredness space produce better transfer performance than difficulty-based progression — specifically on problems the learner has never seen?

These are not aspirational. They are answerable with the data PyBe will generate.

---

*PyBe— VLED Lab, IIT Ropar | June 2026*
*Team Lead: Sneha (sneha-techiee)*
