# SoCLaaS Lab 102 — Prompt Engineering Challenge

Course-agnostic lab on crafting effective prompts. No code build required — purely about prompt quality and analysis.

---

## Lesson Plan

- **Objective:** Students learn that small prompt changes cause large output changes; learn to engineer prompts for specific qualities.
- **Duration:** ~2 hrs, one week's module.
- **Flow:** login + key → experiment with prompts for one question → compare outputs → write short report.
- **Integrity note:** disclose LLM use; graded on analysis, not on any single "correct" output.

---

## Student Handout

### 1. Get your key
Go to `https://soclaas-portal.comp.nus.edu.sg`, login, click "issue key".

### 2. Starter code
```python
import os, requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
url = SOCLAAS_BASE_URL + "/chat/completions"

def ask(prompt):
    resp = requests.post(url,
        headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": "default", "messages": [
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": prompt},
        ]}, timeout=30)
    return resp.json()["choices"][0]["message"]["content"]

print(ask("Explain quantum entanglement."))
```

### 3. The Challenge
Ask the same question **three different ways** (e.g. "in one sentence", "for a 10-year-old", "for a PhD committee"). Paste all three outputs and explain:
- Which prompt gave the most useful output and **why** (specificity? constraints? framing?).
- What changes to the prompt caused the biggest change in output.

### 4. Rubric
| Criterion | Points |
|---|---|
| Three distinct, well-crafted prompts | 3 |
| Clear explanation of why one won | 3 |
| Observation of what changed the output most | 2 |
| Reflection on generalising the lessons | 2 |
