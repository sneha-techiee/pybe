# PyBe — Product & Research Document
### Version 2.0 | Phase 2 | VLED Lab, IIT Ropar

---

> *"Jonassen described structuredness. PyBe computes it."*

---

## Table of Contents

1. [The Problem With Every Existing Learning Platform](#1-the-problem-with-every-existing-learning-platform)
2. [The Central Claim](#2-the-central-claim)
3. [PyBe's Original Contribution](#3-pybes-original-contribution)
4. [The Structuredness Engine](#4-the-structuredness-engine)
5. [The Hybrid Scoring Architecture](#5-the-hybrid-scoring-architecture)
6. [The Cognitive Pipeline](#6-the-cognitive-pipeline)
7. [The Belief Monitoring System](#7-the-belief-monitoring-system)
8. [The Assessment Framework](#8-the-assessment-framework)
9. [System Architecture](#9-system-architecture)
10. [Phase 2 Feature Specification](#10-phase-2-feature-specification)
11. [Team Structure](#11-team-structure)
12. [Git Workflow](#12-git-workflow)
13. [Functional Requirements](#13-functional-requirements)
14. [Non-Functional Requirements](#14-non-functional-requirements)
15. [Research Directions](#15-research-directions)
16. [Documentation Standards](#16-documentation-standards)

---

## 1. The Problem With Every Existing Learning Platform

Every platform that teaches programming makes the same architectural mistake.

They treat learning as **content delivery** — the platform holds knowledge, the learner receives it. Syntax is explained. Examples are shown. Exercises are completed. A certificate is issued.

The result is well-documented and consistent: learners can reproduce patterns they have seen. They cannot reason through problems they have not seen. They pass assessments by recognition, not understanding. Within weeks, most of what was learned is gone.

This is not a content problem. It is a **structural problem.**

The question these platforms never ask is: *what kind of thinking does this problem require?* They measure what the learner knows. They never measure how the learner thinks. They have no model of cognitive progression — only content progression.

PyBe is built to answer the question those platforms never asked.

---

## 2. The Central Claim

> PyBe is not a Python learning platform.
>
> PyBe is a **cognitive learning system** that continuously observes how a person thinks, quantifies the structuredness of their reasoning, introduces precisely calibrated cognitive challenge, and gradually transforms everyday human reasoning into rigorous computational thinking — with Python as the first notation through which that thinking becomes precise.

Notice what is and is not being claimed.

**What is being claimed:**
- The system measures thinking, not just knowledge
- The system adapts to cognitive state, not just performance
- Python is a medium, not the goal
- The goal is a learner who can reason through any problem they have not seen before

**What is not being claimed:**
- That PyBe teaches Python faster
- That PyBe is more engaging or gamified
- That PyBe produces better exam scores

PyBe is optimised for one outcome: **transferable computational thinking.** Everything else is a side effect.

---

## 3. PyBe's Original Contribution

### What Jonassen Gave Us

In 1997, David Jonassen established that problems are not uniformly difficult — they differ in **structuredness**: how many valid solution paths exist, how clearly the goal is defined, how much information is given versus withheld, how many constraints compete.

This was a qualitative contribution. Jonassen described structuredness. He classified problem types. He explained why ill-structured problems are harder to learn from than well-structured ones.

He did not compute structuredness. He did not use it as a control variable. He did not build an adaptive engine around it.

### What PyBe Contributes

PyBe operationalizes structuredness as a **computational parameter**.

Rather than classifying problems into discrete types, PyBe represents every scenario as a point in a continuous structuredness space — defined by four measurable dimensions. Rather than moving learners between levels, PyBe navigates them through this space along a trajectory computed from their cognitive state.

**PyBe's contribution:**

> Transforming a qualitative educational theory into an executable adaptive learning architecture — where structuredness is not a label but a variable, not a taxonomy but an algorithm, not a description but a control signal.

This is the difference between a map and a navigation system. Jonassen drew the map. PyBe navigates it.

---

## 4. The Structuredness Engine

### The Structuredness Score

Every scenario in PyBe — whether from the seed set or dynamically generated — is represented as a vector in structuredness space:

```
Problem {
    ambiguity:              float  // 0.0 = completely unambiguous → 1.0 = fully ambiguous
    solution_space:         float  // 0.0 = one correct answer → 1.0 = infinite valid answers  
    goal_clarity:           float  // 0.0 = goal completely implicit → 1.0 = goal fully explicit
    competing_constraints:  float  // 0.0 = no competing constraints → 1.0 = maximal constraint tension
}
```

These four dimensions produce a single structuredness score:

```
S = f(ambiguity, solution_space, goal_clarity, competing_constraints)

where:

S = w₁·ambiguity + w₂·solution_space + w₃·(1 - goal_clarity) + w₄·competing_constraints

S ∈ [0, 1]
0 = fully structured (one correct answer, explicit goal, no ambiguity)
1 = fully ill-structured (infinite valid answers, implicit goal, maximal constraint tension)
```

Weights w₁ through w₄ are learned from behavioral data and calibrated against the human-scored reference set.

### What the Score Enables

The scenario generator does not think:
> *"Generate a beginner problem about loops."*

It thinks:
> *"Generate a scenario with S = 0.18 involving repetitive real-world reasoning, for a learner whose current cognitive boundary is at S = 0.22."*

This is the difference between a curriculum and an adaptive engine.

### The Progression Model

Learners do not move through levels. They move through structuredness space along a **cognitive trajectory** — a path from S ≈ 0.0 toward S ≈ 1.0, at a pace determined by their individual cognitive state.

The trajectory is not linear. A learner who masters well-structured problems quickly may stall at moderate structuredness for weeks — because the thinking required is qualitatively different, not just harder. PyBe detects this stall and responds by generating scenarios that specifically target the transition.

---

## 5. The Hybrid Scoring Architecture

No single method can reliably assign structuredness scores at scale. PyBe uses a three-layer hybrid architecture where each layer serves a distinct purpose and each layer corrects the layer above it.

### Layer 1 — Human Ground Truth (Calibration Set)

The seed scenario set is manually scored by the research team. Each scenario receives explicit values for all four dimensions. This produces a **calibration corpus** — the reference against which all subsequent scoring is validated.

The calibration corpus is not a training set. It is a standard. It defines what S = 0.15 looks like in practice, what S = 0.67 looks like, what S = 0.92 looks like. Without this anchor, automated scoring has no reference point.

```
seed_scenario {
    id: "sc_001",
    context: "Riya has ₹500 for groceries...",
    human_scores: {
        ambiguity: 0.08,
        solution_space: 0.05,
        goal_clarity: 0.92,
        competing_constraints: 0.04
    },
    S: 0.13,
    scorer: "research_team",
    confidence: "high"
}
```

### Layer 2 — LLM Scoring (Generation Time)

When the system generates a new scenario dynamically, the LLM produces a structuredness score alongside the scenario content. The LLM is prompted with the calibration corpus as reference — it scores relative to known examples, not in isolation.

The LLM score is not accepted directly. It is validated against the nearest neighbors in the calibration corpus. If the LLM scores a scenario at S = 0.45 but its three nearest calibration neighbors average S = 0.28, a discrepancy flag is raised and the scenario is either revised or held for human review.

```
generated_scenario {
    id: "sc_gen_0047",
    context: "A bus conductor has 47 passengers...",
    llm_scores: {
        ambiguity: 0.12,
        solution_space: 0.10,
        goal_clarity: 0.85,
        competing_constraints: 0.08
    },
    S_llm: 0.19,
    nearest_calibration_neighbors: ["sc_001", "sc_004", "sc_011"],
    neighbor_mean_S: 0.16,
    discrepancy: 0.03,   // within threshold — accepted
    status: "validated"
}
```

### Layer 3 — Behavioral Inference (Runtime Correction)

This is the most original component. The system continuously observes learner behavior during a session and infers the *experienced* structuredness of the scenario — which may differ from the assigned score.

**Behavioral signals collected:**

```
behavioral_signals {
    time_to_first_response:    seconds,    // longer → higher experienced S
    revision_count:            integer,    // more revisions → higher experienced S
    hint_requests:             integer,    // more hints → higher experienced S
    response_deviation:        float,      // distance from expected reasoning path
    confidence_markers:        float,      // linguistic confidence in learner text
    abandonment_probability:   float       // estimated from engagement signals
}
```

**Behavioral structuredness inference:**

```
S_behavioral = g(time_to_first_response, revision_count, 
                  hint_requests, response_deviation,
                  confidence_markers)
```

If `S_behavioral` diverges significantly from `S_assigned`, the system updates its model:
- The scenario's score is adjusted toward `S_behavioral`
- The learner's cognitive boundary estimate is recalibrated
- Future scenario selection accounts for the discrepancy

Over time, behavioral signals correct LLM scoring errors. LLM scoring improves from the corrected data. The human calibration corpus grows as confident corrections are reviewed and added.

**The full learning loop:**

```
Human Ground Truth
        ↓  calibrates
LLM Scenario Scoring
        ↓  validated against ground truth
Learner Behavioral Signals
        ↓  correct runtime scores
Corrected Scores Feed Back
        ↓  improve LLM scoring accuracy
        ↓  expand human calibration corpus
        ↑  loop continues
```

This is a self-improving structuredness engine. The system gets more accurate with every learner interaction.

---

## 6. The Cognitive Pipeline

Every learning session in PyBe follows a cognitive pipeline — not a content delivery sequence.

```
┌─────────────────────────────────────────────────────────────────┐
│  STAGE 1 — COGNITIVE STATE ESTIMATION                          │
│  Read learner's current belief graph, misconception profile,   │
│  ZPD boundary estimate, last session behavioral signals        │
│  Output: target structuredness score S_target                  │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│  STAGE 2 — SCENARIO GENERATION                                  │
│  Generate scenario with S ≈ S_target                           │
│  LLM produces scenario + structuredness vector                 │
│  Validate against calibration corpus                           │
│  Output: validated scenario                                     │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│  STAGE 3 — PRODUCTIVE FAILURE WINDOW                           │
│  Learner attempts scenario in natural language                  │
│  No abstraction revealed yet                                   │
│  System collects reasoning text + behavioral signals           │
│  Output: learner reasoning artifact                             │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│  STAGE 4 — BELIEF EXTRACTION                                    │
│  NLP pipeline extracts belief statements from reasoning text   │
│  Cross-references against existing belief graph                │
│  Flags contradictions and overgeneralizations                  │
│  Output: updated belief graph + contradiction flags            │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│  STAGE 5 — SOCRATIC MENTOR RESPONSE                            │
│  LLM generates questions based on learner's actual reasoning   │
│  RAG pipeline grounds explanations in Python literature        │
│  Python code generated from learner's specific reasoning       │
│  No templates. No hardcoded responses.                         │
│  Output: personalized mentor response + generated code         │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│  STAGE 6 — BEHAVIORAL SIGNAL COLLECTION                        │
│  Runtime behavioral data collected throughout session          │
│  S_behavioral computed and compared to S_assigned              │
│  ZPD boundary recalibrated                                     │
│  Output: corrected structuredness score + updated ZPD          │
└──────────────────────────┬──────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────┐
│  STAGE 7 — EPISODIC ANCHORING                                  │
│  Session stored as episode: date + scenario + breakthrough     │
│  Spaced repetition queue updated                               │
│  Belief graph persisted                                        │
│  Output: learner cognitive state updated for next session      │
└─────────────────────────────────────────────────────────────────┘
```

---

## 7. The Belief Monitoring System

### Why Belief Monitoring Matters

A learner's performance on a problem tells you what they did. Their belief graph tells you why they did it — and more importantly, what they will do when they encounter a problem they have never seen before.

PyBe maintains a live belief graph per learner: a structured representation of every claim they have made about how Python works, tagged by concept, timestamped, and linked to the session in which it emerged.

### Belief Extraction

```python
# Learner writes in natural language:
# "I think a variable can only hold one number at a time"

belief_extracted = {
    "statement": "variable holds one value",
    "concept": "variables",
    "session": 3,
    "timestamp": "2026-06-24T14:23:11",
    "confidence": 0.87,     # how strongly stated
    "source": "explicit"    # learner stated directly vs implied
}
```

### Contradiction Detection — The Original Contribution

When a learner holds two beliefs that are individually plausible but logically incompatible, the contradiction detector flags them and engineers a targeted resolution scenario.

```python
# Session 3 belief:
belief_A = {
    "statement": "a variable can only hold one value",
    "concept": "variables", "session": 3
}

# Session 6 belief:
belief_B = {
    "statement": "a list holds many values",
    "concept": "lists", "session": 6
}

# Contradiction:
# x = [1, 2, 3] is a variable (belief_A domain)
# containing a list of many values (belief_B domain)
# belief_A says this is impossible
# belief_B says the container holds many things
# but belief_A says the variable — which IS the container — holds only one

contradiction = {
    "belief_A": belief_A,
    "belief_B": belief_B,
    "conflict": "variable-as-container vs list-as-many-valued",
    "resolution_concept": "variables are names pointing to objects; objects have types",
    "scenario_target_S": 0.31,
    "resolution_method": "socratic"
}
```

The system generates a scenario where the learner walks into this contradiction themselves. The system never corrects. It only asks questions until the learner resolves the conflict independently.

This operationalizes Piaget's cognitive conflict mechanism: schema reconstruction triggered by the learner's own reasoning colliding with evidence they cannot ignore.

### Overgeneralization Detection

Distinct from contradiction: a correctly-learned concept applied outside its valid domain.

```python
overgeneralization = {
    "learned_correctly": "= means assignment",
    "domain": "variable assignment statements",
    "invalid_application": "if x = 5:",
    "domain_violated": "boolean comparison context",
    "resolution_concept": "= and == are different operators for different contexts"
}
```

---

## 8. The Assessment Framework

Standard assessments test knowledge. Knowledge can be copied, searched, or generated by AI.

PyBe tests thinking. Thinking cannot be copied because it is not stored anywhere except in the learner's own cognitive architecture.

### Cheat-Proof by Design

Every assessment scenario is generated fresh by combining:

```
scenario = generate(
    context   = random.choice(real_world_contexts),
    constraint = random.choice(constraint_types),
    actor      = random.choice(human_actors),
    S_target   = learner.current_structuredness_boundary
)
```

No two sessions produce the same problem. There is nothing to memorize, nothing to search for, nothing to copy. The only way to perform well is to think.

### Transfer Probes

Mid-session, the system modifies one constraint and observes adaptation:

> *"You solved it for ₹500. Now the budget changes every day. Does your approach still work?"*

Genuine understanding transfers immediately. Pattern-matched memorization cannot adapt because the pattern no longer matches.

### Misconception Traps

The assessment is designed around the learner's own belief graph. If the system knows the learner believes "loops always need a counter," it generates a scenario where a counter-based approach is specifically the wrong tool. The trap is personalized. It cannot be prepared for because it is built from the learner's own misconceptions.

### The Thinking Fingerprint

Assessment output is not a score. It is a cognitive profile:

```python
thinking_fingerprint = {
    "decomposition":     float,  # broke problem into components before attempting?
    "transfer":          float,  # adapted when constraint changed?
    "explanation":       float,  # could explain simply to non-technical person?
    "trap_resistance":   float,  # avoided their known misconception?
    "abstraction_climb": float,  # how far toward abstraction without prompting?
    "S_handled":         float   # highest structuredness successfully reasoned through
}
```

No two learners have the same fingerprint. No two sessions produce the same fingerprint. Progress is measured as fingerprint evolution over time, not as points accumulated.

---

## 9. System Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        LEARNER INTERFACE                         │
│                       React + Vite (JS)                          │
│                                                                  │
│  Scenario View  │  Reasoning Input  │  Mentor Response           │
│  Abstraction Ladder UI              │  Session Reflection        │
│  Belief Evolution Dashboard         │  Progression Map           │
│  Episodic Memory Timeline           │  Thinking Fingerprint      │
└────────────────────────┬─────────────────────────────────────────┘
                         │ HTTP / WebSocket
┌────────────────────────▼─────────────────────────────────────────┐
│                      API LAYER                                    │
│                  Express + Node.js (JS)                           │
│                                                                   │
│  Session Router  │  Cognitive State Manager  │  ZPD Calculator   │
│  Structuredness Navigator  │  Spaced Repetition Scheduler        │
│  Behavioral Signal Collector  │  Belief Graph API               │
└────────────────────────┬─────────────────────────────────────────┘
                         │ REST
┌────────────────────────▼─────────────────────────────────────────┐
│                   INTELLIGENCE LAYER                              │
│                   FastAPI (Python)                                │
│                                                                   │
│  ┌─────────────────┐  ┌──────────────────┐  ┌────────────────┐  │
│  │  RAG PIPELINE   │  │  BELIEF ENGINE   │  │  STRUCTUREDNESS│  │
│  │                 │  │                  │  │  ENGINE        │  │
│  │  ChromaDB       │  │  Belief Extract  │  │                │  │
│  │  Embeddings     │  │  Contradiction   │  │  LLM Scorer    │  │
│  │  Python Books   │  │  Detection       │  │  Behavioral    │  │
│  │  LLM Mentor     │  │  Overgeneralize  │  │  Inference     │  │
│  │  Socratic Engine│  │  Resolution Gen  │  │  Calibration   │  │
│  └─────────────────┘  └──────────────────┘  └────────────────┘  │
└────────────────────────┬─────────────────────────────────────────┘
                         │
┌────────────────────────▼─────────────────────────────────────────┐
│                      DATA LAYER                                   │
│                        MongoDB                                    │
│                                                                   │
│  belief_graph      │  session_history    │  structuredness_scores │
│  calibration_corpus│  behavioral_signals │  spaced_repetition_queue│
│  thinking_fingerprints  │  episodic_timeline  │  contradiction_log  │
└──────────────────────────────────────────────────────────────────┘
```

### Technology Stack

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | React + Vite | Learner interface, dashboards |
| API | Express + Node.js | Session management, routing, ZPD logic |
| Intelligence | FastAPI (Python) | RAG, belief engine, structuredness scorer |
| Vector Store | ChromaDB | Python literature embeddings |
| LLM | Claude API / Gemini API | Mentor, Socratic engine, scenario generation, scoring |
| Embeddings | all-MiniLM-L6-v2 | Semantic retrieval |
| Database | MongoDB | All persistent learner data |

---

## 10. Phase 2 Feature Specification

### Track 1 — AI Mentor + RAG Pipeline

**Replaces:** `learningEngine.js` — keyword matching + 5 static templates

**Builds:**

*Real LLM Mentor*
The mentor reads what the learner actually wrote. Every response is generated from the learner's specific reasoning about their specific scenario. No template responses exist in the system.

*RAG Pipeline*
Four to five foundational Python texts loaded into ChromaDB with semantic embeddings. When a concept requires explanation, the system retrieves the most relevant passage and grounds the response in authoritative literature. The learner receives an explanation anchored to real educational content — not to whatever the LLM generates from training data alone.

*Socratic Question Engine*
Questions are not pre-written. They are generated from the learner's actual response. The system identifies the weakest link in the learner's reasoning and generates a question that targets exactly that weakness.

*Python Historical Arc*
Every concept is introduced through the problem that caused it to be invented:

| Concept | The Problem That Created It |
|---|---|
| Variables | Memory addresses were unmemorable. Names were invented. |
| Loops | Repetition was written out manually. Abstraction over repetition was invented. |
| Functions | Identical blocks appeared everywhere. Named reuse was invented. |
| Lists | Related items lived in separate variables. Containers were invented. |
| Dictionaries | Position-indexed containers were unintuitive. Named-key containers were invented. |
| Classes | Functions and their data travelled together informally. Formal packaging was invented. |
| Exceptions | Silent failures were undebuggable. Explicit failure signals were invented. |

*Code Generation*
Python code is generated from the learner's reasoning — not selected from a template library. The code reflects the learner's variable names, their scenario's context, their chosen approach. They look at it and recognize their own thinking.

---

### Track 2 — Belief Monitoring + Contradiction Engine

**The original research contribution.**

Full specification in Section 7.

Key deliverables:
- Belief extractor — NLP pipeline for belief statement extraction from natural language
- Belief graph — persistent per-learner, timestamped, concept-tagged
- Contradiction detector — cross-session logical conflict identification
- Overgeneralization detector — valid-concept boundary violation detection  
- Contradiction scenario generator — targeted scenario engineering from conflict profile
- Socratic resolution engine — question sequence that leads learner to self-resolution

---

### Track 3 — Structuredness Engine + Assessment

Full specification in Sections 4, 5, and 8.

Key deliverables:
- Structuredness scoring system — four-dimensional vector representation
- Hybrid scoring pipeline — human calibration + LLM scoring + behavioral correction
- ZPD calculator — cognitive boundary estimation from session history
- Scenario generator — S-targeted scenario production
- Transfer probe engine — mid-session constraint modification
- Misconception trap designer — personalized from learner belief graph
- Thinking fingerprint scorer — five-dimensional cognitive profile output

---

### Track 4 — Frontend + Dashboard + Documentation

Key deliverables:

*Abstraction Ladder UI*
```
Python Code              ← learner arrives here last
Named Abstraction
Pattern Recognition
Mental Model
Concrete Experience      ← learner begins here
```
Visual, interactive, non-skippable. The learner sees where they are in real time.

*Belief Evolution Dashboard*
Not a progress bar. A graph of how the learner's beliefs have changed over time. Which beliefs were corrected. Which contradictions were resolved. Where the breakthroughs happened.

*Thinking Fingerprint Visualization*
Radar chart showing all five cognitive dimensions. Evolution across sessions visible.

*Episodic Memory Timeline*
*"The list comprehension clicked on July 3rd during the train booking scenario."*

*Structuredness Progression Map*
Shows the learner's trajectory through structuredness space — not through levels, through a continuous cognitive landscape.

*Full Documentation Suite*
- `README.md` — setup, run, test
- `PRODUCT.md` — this document
- `ARCHITECTURE.md` — full system design with diagrams
- `RESEARCH.md` — academic framing, citations, contribution claims
- `API.md` — all endpoints, request/response schemas
- `BELIEF_ENGINE.md` — belief extraction, contradiction detection specification
- `STRUCTUREDNESS.md` — scoring formula, calibration methodology, validation approach
- Inline documentation on every non-trivial function 
### Additional Frontend UI/UX Ideas

These ideas focus on making PyBe more interactive, visually engaging, and suitable for learners of different ages and skill levels.

#### Interactive Learning Journey
- Display the learner's progress as a visual journey instead of a traditional progress bar.
- Unlock new milestones through completed reasoning tasks.
- Highlight upcoming concepts and completed achievements.

#### AI Mentor Chat Panel
- Persistent chat interface with conversation history.
- Suggested follow-up questions based on learner responses.
- Quick explanation buttons for concepts, examples, and hints.

#### Personalized Home Dashboard
- Welcome message based on learner progress.
- Resume last learning session.
- Daily learning goals and recommended scenarios.

#### Concept Relationship Map
- Interactive graph connecting Python concepts.
- Selecting a concept highlights related concepts and prerequisite knowledge.
- Visual learning path between beginner and advanced topics.

#### Interactive Code Playground
- Built-in Python editor for testing generated code.
- Run, reset, and compare code versions.
- Highlight syntax and explain errors in simple language.

#### Achievement and Motivation System
- Earn badges for completing milestones.
- Display learning streaks and consistency rewards.
- Celebrate concept mastery with simple animations.

#### Accessibility Features
- Light mode and dark mode.
- Adjustable font size.
- High-contrast mode.
- Keyboard-friendly navigation.
- Color-blind friendly visualization.

#### Responsive Mobile Experience
- Optimized layouts for desktop, tablet, and mobile.
- Touch-friendly controls.
- Smooth transitions and animations across devices.

#### Session Summary Card
After every completed session, display:
- Concepts learned
- Mistakes identified
- AI mentor suggestions
- Next recommended scenario
- Estimated mastery improvement

#### Interactive Onboarding
- Short guided tour for first-time users.
- Explain the learning workflow step by step.
- Allow users to skip or revisit the tutorial anytime.
---

## 11. Team Structure

| Track | Domain | Core Deliverable |
|---|---|---|
| Track 1 | AI Mentor + RAG Pipeline | LLM integration, RAG over Python books, Socratic engine, code generation |
| Track 2 | Belief Monitoring + Contradiction Engine | Belief extractor, contradiction detector, resolution scenario generator |
| Track 3 | Structuredness Engine + Assessment | Structuredness scorer, hybrid pipeline, ZPD, thinking fingerprint |
| Track 4 | Frontend + Dashboard + Documentation | Full UI, all dashboards, complete documentation suite |

**Team Lead:** Sneha (`sneha-techiee`)
Responsibilities: fork management, PR review, integration quality, research direction, final PR to upstream

---

## 12. Git Workflow

```
prof-upstream/pybe                    ← Professor's original repository
        ↑  single clean PR when ready
sneha-techiee/pybe                    ← Team hub — all integration happens here
        ↑  PRs from all teammates
teammate-1/pybe  (Track 1)
teammate-2/pybe  (Track 2)
teammate-3/pybe  (Track 3)
```

**Branch naming convention:**
```
track1/rag-pipeline
track1/socratic-engine
track2/belief-extractor
track2/contradiction-detector
track3/structuredness-scorer
track3/assessment-engine
track4/abstraction-ladder-ui
track4/belief-dashboard
```

**PR requirements — non-negotiable:**
Every PR must include:
- What was built
- Why it exists — which part of the cognitive pipeline it serves
- Which research principle it operationalizes
- How to test it — specific test cases, expected outputs
- Any assumptions made that the team lead should know

No PR without documentation gets reviewed. No PR without tests gets merged.

---

## 13. Functional Requirements

**Structuredness Engine**
1. Every scenario shall be represented as a four-dimensional structuredness vector
2. The system shall maintain a human-scored calibration corpus as ground truth
3. LLM-assigned scores shall be validated against the calibration corpus before acceptance
4. Behavioral signals shall be collected throughout every session
5. Behavioral structuredness inference shall correct LLM-assigned scores at runtime
6. Corrected scores shall feed back into the calibration corpus after human review

**Cognitive Pipeline**
7. The system shall estimate each learner's cognitive boundary before each session
8. Scenario selection shall target S ≈ learner's current cognitive boundary
9. The productive failure window shall precede all abstraction revelation
10. Python code shall be generated from learner reasoning — no templates

**Belief Monitoring**
11. Belief statements shall be extracted from learner natural language every session
12. Beliefs shall be stored with concept tag, session ID, timestamp, and confidence score
13. Contradiction detection shall run after every session across the full belief graph
14. Overgeneralization detection shall identify valid concepts applied outside their domain
15. Contradiction scenarios shall be generated targeting the specific conflict identified
16. Socratic resolution dialogue shall continue until the learner self-resolves the conflict

**Assessment**
17. Every assessment scenario shall be uniquely generated — no reuse across sessions
18. Transfer probes shall modify one constraint mid-session for every assessment
19. Misconception traps shall be derived from the learner's current belief graph
20. Thinking fingerprint shall be computed across all five dimensions per session
21. Spaced repetition queue shall schedule scenario resurfacing at day 1, 3, 7, 21

**RAG Pipeline**
22. The system shall index at minimum four foundational Python texts
23. Every concept explanation shall retrieve and cite the most relevant passage
24. Retrieved passages shall ground LLM responses — LLM shall not respond from training data alone on concept explanations

---

## 14. Non-Functional Requirements

| Requirement | Specification |
|---|---|
| LLM response latency | Under 4 seconds end-to-end |
| RAG retrieval latency | Under 500ms |
| Structuredness scoring latency | Under 1 second |
| Belief graph update | Synchronous within session, persisted before session close |
| Behavioral signal collection | Real-time, non-blocking |
| Session data persistence | Zero data loss on session close |
| System startup | Single `npm run dev` command for full stack |
| Scenario uniqueness | Probabilistic guarantee — collision rate under 0.1% per learner |
| Calibration corpus integrity | Human-scored entries are immutable once validated |
| Documentation coverage | Every public function documented; every module has a README |

---

## 15. Research Directions

PyBe Phase 2 opens the following research questions, each tractable within the fellowship timeline:

**On structuredness measurement:**
- Can the four-dimensional structuredness vector reliably predict learner cognitive load?
- Does behavioral structuredness inference converge to human-assigned scores over time?
- Are the four dimensions orthogonal, or does correlation between them suggest a simpler underlying model?

**On belief monitoring:**
- Can NLP reliably extract implicit belief statements from informal learner text?
- Does contradiction-targeted scenario generation accelerate misconception resolution compared to standard instruction?
- Is there a relationship between structuredness level and misconception formation rate?

**On learning outcomes:**
- Does structuredness-calibrated progression produce better transfer performance than difficulty-calibrated progression?
- Does the thinking fingerprint predict future performance on unseen problem types?
- Does episodic anchoring improve long-term retention compared to platforms without it?

These questions are not aspirational. They are answerable with the data PyBe Phase 2 will generate. Each has the structure of a publishable contribution.

---

## 16. Documentation Standards

Documentation is not optional. It is not written after the code. It is written alongside the code, as part of the same commit.

**Every feature must document:**
- What it does — one precise sentence
- Why it exists — which part of the cognitive pipeline it serves
- Which principle it operationalizes — structuredness scoring, belief monitoring, ZPD, productive failure, spaced repetition, episodic memory
- How to test it — specific inputs, expected outputs, edge cases

**Every module must have:**
- A header comment explaining its role in the system
- Type annotations on all functions
- Docstrings on all non-trivial functions
- A section in the relevant documentation file

**The standard:**
If a new team member reads the documentation without reading the code, they should be able to explain what the system does, why it is designed the way it is, and what research claim each component supports.

That is the standard. Write to it.

---

*PyBe Phase 2 — VLED Lab, IIT Ropar | June 2026*
*Team Lead: Sneha | github.com/sneha-techiee*
*Research Supervisor: Prof. Sudarshan Iyengar*
