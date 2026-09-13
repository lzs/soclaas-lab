# SoCLaaS Lab 110 — Agent Memory

Course-agnostic lab on giving an agent memory across turns. Students confront the model's statelessness and build a simple persistent memory layer.

---

## Lesson Plan

- **Objective:** Students see that each API call is stateless, then build a memory store so an agent remembers conversation history and facts.
- **Duration:** ~2–3 hrs.
- **Flow:** key → stateless demo (forgetting) → build a store → agent that recalls past facts → compare.
- **Integrity note:** disclose LLM use; graded on the memory design.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code
```python
import os, json, requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
URL = SOCLAAS_BASE_URL + "/chat/completions"

MEMORY_FILE = "memory.json"   # persists facts across turns

def load():
    try:
        return json.load(open(MEMORY_FILE))
    except (FileNotFoundError, json.JSONDecodeError):
        return {}

def save(mem):
    json.dump(mem, open(MEMORY_FILE, "w"), indent=2)

def ask(prompt, mem):
    # inject remembered facts into the system prompt
    facts = "\n".join(f"{k}: {v}" for k, v in mem.items())
    resp = requests.post(URL, headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": "default", "messages": [
            {"role": "system", "content": f"You remember: {facts}"},
            {"role": "user", "content": prompt}], timeout=30})
    return resp.json()["choices"][0]["message"]["content"]

mem = load()

# --- turn 1: tell the agent facts, then ask it to forget ---
facts = "My name is Zeng. I prefer morning sessions. I like spicy food."
print(ask(f"Remember these: {facts}", mem))

# --- persist a "fact store" by extracting key:val pairs ---
# (here we do it explicitly; an agent could extract them with a tool call)
mem.update({"name": "Zeng", "preferred_time": "morning", "food": "spicy"})
save(mem)

# --- turn 2 (new run): does the agent remember? ---
mem = load()
print(ask("What's my name and what food do I like?", mem))
```

### 3. The Challenge
Build an agent that (a) stores facts it learns across turns, (b) lets a user query "do you remember...?", and (c) recovers from a model that *hallucinates* a forgotten fact. Show the memory file, a multi-turn log, and explain how you'd prevent false memory recall.

### 4. Rubric
| Criterion | Points |
|---|---|
| Persistent memory store works across runs | 3 |
| Facts injected into context correctly | 2 |
| Handles hallucinated recall | 3 |
| Reflection on memory limits (capacity, staleness) | 2 |
