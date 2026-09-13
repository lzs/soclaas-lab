# SoCLaaS Lab 101 — Prompt Engineering & Agent Skills

**Turnkey lab kit** — course-agnostic, ~2 hrs of work, fits into any existing course as one week's module.

**Why this works across courses:** the skills (prompt engineering, one programmatic API call, a small agent loop, cost/accuracy awareness) are transferable to every discipline. Faculty don't add subject content — they reuse the kit as-is.

---

## Lesson Plan (one page)

- **Objective:** Students learn to use the SoCLaaS LLM API — prompt engineering, one programmatic call, and a tiny agent.
- **Duration:** ~2 hrs total, one week's module.
- **Flow:**
  1. Login + create key at the portal (5 min)
  2. Make the first API call (5 min)
  3. Prompt-engineering exercise
  4. Build a tiny agent / tool loop
  5. Reflection on cost and accuracy
- **Integrity note:** LLM use must be disclosed per academic-policy. Graded on skill, not polish.

---

## Student Handout

### 1. Get your key
Go to `https://soclaas-portal.comp.nus.edu.sg`, login, click "issue key". Done.

### 2. First call
```python
import os
import requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
url = SOCLAAS_BASE_URL + "/chat/completions"

resp = requests.post(
    url,
    headers={"Authorization": f"Bearer {API_KEY}"},
    json={"model": "default", "messages": [
        {"role": "user", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Summarize quantum entanglement in 3 bullets."},
    ]},
    timeout=30,
)
print(resp.json())
```

### 3. Pick one challenge

**A. Prompt critique** — write 3 different prompts for the same question; compare the outputs; explain why the best one won.

**B. Study buddy** — use the API to generate a flashcard or quiz set from a reading you own, then self-test with it.

**C. Tiny assistant** — build a small tool that helps you study or work in your own field (a to-do summarizer, a code explainer, a citation formatter). Open-ended — good for a capstone.

---

## Rubric (simple)

| Criterion | Points |
|---|---|
| First API call works | 2 |
| Prompt quality / iteration shown | 3 |
| Tool use or extension (challenge C) | 3 |
| Reflection on cost + accuracy | 2 |
