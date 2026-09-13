# SoCLaaS Lab 109 — Multi-Agent Systems

Course-agnostic lab on building multiple agents that coordinate. Students implement agent-to-agent routing between two specialist agents.

---

## Lesson Plan

- **Objective:** Students build a multi-agent system where specialist agents (e.g. researcher + reviewer) talk to each other to solve a task.
- **Duration:** ~3 hrs (or a capstone).
- **Flow:** key → define a "planner" and a "worker/evaluator" agent → pass work between them → observe the emergent solution.
- **Integrity note:** disclose LLM use; graded on the coordination design.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code (planner ↔ worker loop)
```python
import os, requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
URL = SOCLAAS_BASE_URL + "/chat/completions"

def call(system, user, name):
    resp = requests.post(URL, headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": "default", "messages": [
            {"role": "system", "content": system},
            {"role": "user", "content": user}], timeout=30})
    return resp.json()["choices"][0]["message"]["content"]

task = "Design a secure login flow for a small web app."

# planner proposes; reviewer grades; loop until the reviewer is happy
plan, score = "", 0
for _ in range(4):
    plan = call("You are a security engineer. Design a login flow.", task)
    score = int(call("You are a strict reviewer. Score the following 1-10 and give one improvement.", plan))
    print(f"score = {score}")
    if score >= 8:
        break
    improvement = call("Give one concrete improvement to:", plan)
    task = f"{task}\n\nIncorporate this improvement: {improvement}"

print("FINAL PLAN:")
print(plan)
```

### 3. The Challenge
Pick a task and define at least **two specialist agents** with different roles (e.g. a *generator* and a *critic*, or a *planner* and an *executor*). Have them iterate until a quality bar is met. Show the agent log and explain what each agent contributed. Then try adding a third agent (e.g. a summariser) and compare.

### 4. Rubric
| Criterion | Points |
|---|---|
| Two+ agents with distinct roles | 3 |
| Real agent-to-agent coordination/iteration | 3 |
| Log shows division of labour | 2 |
| Reflection on failure modes (loops, drift) | 2 |
