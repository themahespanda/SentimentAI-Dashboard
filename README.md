# Rule-Based Sentiment & Emotion Analysis Agent

An educational, interactive, explainable Natural Language Processing (NLP) agent built strictly using **rule-based heuristics, predefined lexical dictionaries, negation scope tracking, and emotion recognition**.

> [!IMPORTANT]
> **Zero Machine Learning / External AI**: This project does not use machine learning models, OpenAI APIs, LLM calls, or cloud classification services. All decisions are deterministic, transparent, and explainable, demonstrating foundational NLP and algorithmic linguistics.

---

## 🌟 Key Features

1. **Deterministic Sentiment Classification**:
   - Classifies sentences into **Very Positive**, **Positive**, **Neutral**, **Negative**, **Very Negative**, and **Mixed**.
   - Predefined keyword lexicon with explicit weights ($-3$ to $+3$).
   - Intensifiers (`very`, `extremely`, `so`) and diminishers (`slightly`, `hardly`).
2. **Contextual Negation Handling**:
   - Detects negation words: `not`, `never`, `no`, `don't`, `didn't`, `can't`, `isn't`, `wasn't`, etc.
   - Applies an active negation window over subsequent tokens.
   - Punctuation clause boundaries (`,`, `.`, `;`, `!`, `?`) and contrastive conjunctions cleanly reset negation scope.
   - Flips positive words to negative (`not good`: $+1 \to -1$).
   - Inverts negative words to positive or neutral (`not unhappy`: $-1 \to +1$).
3. **Emotion Detection**:
   - Detects distinct emotional states:
     - **Happiness 😊** (`happy`, `joy`, `delighted`, `glad`, etc.)
     - **Sadness 😢** (`sad`, `unhappy`, `sorrow`, `disappointed`, etc.)
     - **Anger 😡** (`angry`, `mad`, `furious`, `terrible`, `hate`, etc.)
     - **Fear 😨** (`afraid`, `scared`, `fearful`, `anxious`, etc.)
     - **Love ❤️** (`love`, `adore`, `cherish`, `fond`, etc.)
     - **Excitement 🤩** (`excited`, `amazing`, `awesome`, `thrilled`, etc.)
   - Negation properly suppresses emotional triggers (e.g. `not happy` suppresses Happiness).
4. **Transparent "Rule Confidence"**:
   - Clearly labeled **Rule Confidence** (not ML confidence).
   - Computed from matched keyword density, keyword counts, and absolute score intensity.
   - Exactly **0%** when no keywords are detected.
5. **Interactive Portfolio Dashboard**:
   - Responsive layout supporting Desktop, Laptop, Tablet, and Mobile.
   - Persistent **Light / Dark Mode** toggle with system preference fallback.
   - **Clickable Example Sentences** for quick testing.
   - **"How was this classified?"** expandable section displaying tokenized chips, negation triggers, and step-by-step rule reasoning.
   - **Conversation History** with "You" and "Agent" message bubbles.
   - **Live Statistics Dashboard** with total message count, average sentiment score, and visual horizontal distribution bars.
   - **Session Summary** card updating reactively after every message.
   - Full client-side `localStorage` persistence.
6. **Dual Implementations (Web + Python)**:
   - Interactive ES6 web application (`index.html`, `js/`, `css/`).
   - Standalone Python CLI and library (`python/sentiment_agent.py`) with unit tests (`python/test_agent.py`).

---

## 🏛️ NLP Pipeline Architecture

```
USER INPUT
    ↓
Normalize Text (whitespace, quotes, apostrophes)
    ↓
Tokenize Text (word tokens, clause punctuation boundaries)
    ↓
Detect Negation Words & Window Scope (1–3 tokens)
    ↓
Detect Intensifiers & Diminishers
    ↓
Match Sentiment Lexicon & Invert/Scale Weights
    ↓
Detect Emotion Keywords (with negation suppression)
    ↓
Calculate Rule Confidence %
    ↓
Evaluate Classification Rules (Thresholds & Contrastive Mixed Logic)
    ↓
Build Explainable Step-by-Step Trace
    ↓
Update Conversation History & Reactive Statistics (LocalStorage)
```

---

## 📊 Predefined Lexicon & Scoring Rules

### Keyword Weights
| Category | Weight | Sample Keywords |
| :--- | :---: | :--- |
| **Very Positive** | `+3` | `amazing`, `excellent`, `awesome`, `fantastic`, `wonderful`, `brilliant`, `outstanding`, `perfect` |
| **Positive** | `+2` | `great`, `love`, `happy`, `pleased`, `joy`, `excited`, `helpful`, `clean`, `fast`, `reliable` |
| **Moderate Positive**| `+1` | `good`, `nice`, `like`, `fine`, `decent`, `pleasant`, `glad`, `neat`, `useful`, `okay` |
| **Moderate Negative**| `-1` | `bad`, `poor`, `dislike`, `annoying`, `slow`, `confusing`, `unhappy`, `ugly`, `weak` |
| **Negative** | `-2` | `sad`, `disappointing`, `disappointed`, `frustrated`, `useless`, `broken`, `rude`, `angry`, `afraid` |
| **Very Negative** | `-3` | `terrible`, `awful`, `horrible`, `worst`, `hate`, `dreadful`, `disastrous`, `furious` |

### Classification Thresholds
- **Score $\ge +3$**: `Very Positive`
- **Score $+1$ to $+2$**: `Positive`
- **Score $0$**: `Neutral` (or when no keywords match)
- **Score $-1$ to $-2$**: `Negative`
- **Score $\le -3$**: `Very Negative`
- **Mixed**: Triggered when both strong positive ($\ge +1.5$) and negative ($\le -1.5$) components exist, OR when a contrastive conjunction (`but`, `however`, `although`, `yet`) links opposing clauses (e.g. *"The product is great but the service is terrible."*).

---

## 🚀 Getting Started

### 1. Web Application (Interactive Dashboard)
You can open `index.html` directly in any modern web browser, or serve it using Python's built-in HTTP server:

```bash
# Serve locally
python -m http.server 8080 --directory "C:\Users\mahes\.gemini\antigravity\scratch\rule-based-sentiment-agent"
```
Then visit: `http://localhost:8080`

### 2. Run Automated Node.js Tests
```bash
cd "C:\Users\mahes\.gemini\antigravity\scratch\rule-based-sentiment-agent"
node tests/run_tests.js
```

### 3. Run Python CLI Agent & Unit Tests
```bash
# Run interactive CLI
python python/sentiment_agent.py

# Run unit tests
python -m unittest discover -s python -p "test_*.py"
```

---

## 🧪 Verified Test Cases

All 8 canonical test cases from the project specification pass with 100% compliance:

| # | Input Sentence | Classification | Emotion | Score | Rule Confidence |
|---|:---|:---:|:---:|:---:|:---:|
| 1 | `"I love this product."` | **Positive** | Love ❤️ | `+2` | `83%` |
| 2 | `"This product is terrible."` | **Very Negative** | Anger 😡 | `-3` | `88%` |
| 3 | `"The product is good."` | **Positive** | None 😐 | `+1` | `74%` |
| 4 | `"The product is not good."` | **Negative** (Negated) | None 😐 | `-1` | `73%` |
| 5 | `"The product is great but the service is terrible."` | **Mixed** (Contrastive) | Anger 😡 | `-1` | `98%` |
| 6 | `"This is an ordinary product."` | **Neutral** | None 😐 | `0` | `0%` |
| 7 | `""` (Empty input) | *Validation Error* | — | — | — |
| 8 | Multiple messages session | *Reactive Stats & Summary* | — | `Avg Score` | *Dynamic* |

---

## 📂 Project Structure

```
rule-based-sentiment-agent/
├── index.html              # Main interactive UI dashboard
├── package.json            # Node project configuration
├── README.md               # Documentation & technical specifications
├── css/
│   └── style.css           # Modern CSS styling (Light/Dark themes, responsive)
├── js/
│   ├── app.js              # UI controller & DOM event handling
│   ├── emotions.js         # Predefined emotion keyword dictionaries
│   ├── lexicon.js          # Sentiment keyword weights, intensifiers, conjunctions
│   ├── negation.js         # Negation lexicon & window tracking rules
│   ├── sentimentEngine.js  # Core NLP pipeline & explainability generator
│   ├── stats.js            # Session statistics & summary metrics
│   └── storage.js          # LocalStorage persistence service
├── tests/
│   └── run_tests.js        # Node.js automated test suite
└── python/
    ├── sentiment_agent.py  # Standalone Python CLI & library
    └── test_agent.py       # Python unit tests
```
