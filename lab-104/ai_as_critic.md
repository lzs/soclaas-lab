# SoCLaaS Lab 104 — AI As Critic & Tutor

Course-agnostic lab on using the LLM to review and improve the student's own work — a self-study skill useful in every field.

---

## Lesson Plan

- **Objective:** Students use the API to critique their own work against a rubric, then revise.
- **Duration:** ~2 hrs.
- **Flow:** key → submit own work → ask for rubric-based feedback → revise → reflect.
- **Integrity note:** disclose LLM use; graded on the student's own judgment, not the AI's grading.

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

def ask(prompt):
    resp = requests.post(url,
        headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": "default", "messages": [
            {"role": "system", "content": "You are a rigorous tutor."},
            {"role": "user", "content": prompt},
        ]}, timeout=30)
    return resp.json()["choices"][0]["message"]["content"]

work = open("my_essay.txt").read()
rubric = "clarity 30%, correctness 40%, evidence 30%"
print(ask(f"Grade the following against this rubric: {rubric}\n\n{work}"))
```

### 3. The Challenge
Submit an assignment, exam answer, or code from one of YOUR courses. Ask the API to:
- Grade it against a rubric you define.
- List the 3 strongest weaknesses with concrete fixes.
Then **revise your work** and write a short note: what feedback did you accept, what did you reject, and why?

### 4. Rubric
| Criterion | Points |
|---|---|
| Correctly framed the critique prompt | 2 |
| Review of feedback (accepted vs rejected, with reason) | 4 |
| Visible improvement in the revised work | 2 |
| Honest reflection on the AI's limitations | 2 |
