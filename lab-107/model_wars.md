# SoCLaaS Lab 107 — Model Wars

Course-agnostic lab on comparing outputs across models. Teaches that "best" depends on the task.

---

## Lesson Plan

- **Objective:** Students compare models on the same task and judge trade-offs (speed, quality, cost, style).
- **Duration:** ~2 hrs.
- **Flow:** key → ask the same question of several models → blind-compare → decide a winner per task.
- **Integrity note:** disclose LLM use; graded on judgment and methodology, not a "correct" winner.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code
```python
import os, requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
url = SOCLAAS_BASE_URL + "/chat/completions"

def ask(model, prompt):
    resp = requests.post(url, headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": model, "messages": [{"role": "user", "content": prompt}], timeout=30})
    return resp.json()["choices"][0]["message"]["content"]

for m in ["model-a", "model-b", "model-c"]:   # swap for real SoCLaaS model names
    print(f"=== {m} ===")
    print(ask(m, "Explain recursion as if to a beginner."))
    print()
```

### 3. The Challenge
Choose 3 SoCLaaS models. Ask them the same 3 questions (one factual, one creative, one technical). Blind-compare the outputs and decide:
- Which model is best for **accuracy**.
- Which is best for **creativity**.
- Which is best for **conciseness / speed**.
Report *why* for each verdict.

### 4. Rubric
| Criterion | Points |
|---|---|
| 3 models tested on 3 task types | 3 |
| Blind comparison with reasoned verdicts | 4 |
| Distinction between "best overall" and "best for the task" | 2 |
| Reflection on model choice in production | 1 |
