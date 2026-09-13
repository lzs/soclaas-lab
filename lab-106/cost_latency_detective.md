# SoCLaaS Lab 106 — Cost & Latency Detective

Course-agnostic lab on measuring tokens, latency, and cost. Teaches efficiency and keeps SoCLaaS spend honest.

---

## Lesson Plan

- **Objective:** Students measure how model calls cost time and tokens, and learn to optimize.
- **Duration:** ~2 hrs.
- **Flow:** key → run 100 calls → record latency, tokens, cost → analyze → optimize one prompt and re-measure.
- **Integrity note:** graded on the analysis, not on getting the lowest number.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code
```python
import os, time, requests, statistics

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
url = SOCLAAS_BASE_URL + "/chat/completions"

# Approx price per token — check your SoCLaaS pricing and adjust
PRICE_PER_TOKEN = 0.000001   # USD

def run(prompt, n):
    times, tokens = [], 0
    for _ in range(n):
        t0 = time.time()
        resp = requests.post(url, headers={"Authorization": f"Bearer {API_KEY}"},
            json={"model": "default", "messages": [
                {"role": "user", "content": prompt}], timeout=60})
        body = resp.json()
        times.append(time.time() - t0)
        tokens += body.get("usage", {}).get("total_tokens", 0)
    return times, tokens

times, tokens = run("Summarize the causes of the Second World War in 50 words.", 100)
print("avg latency: %.3fs" % statistics.mean(times))
print("total tokens: %d" % tokens)
print("total cost: $%.4f" % (tokens * PRICE_PER_TOKEN))
```

### 3. The Challenge
Run 100 calls of the same prompt; record latency, tokens, and cost. Then write a prompt that gets the *same quality* output in fewer tokens or faster. Re-measure and show the improvement. Explain where you'd cut (system prompt, max tokens, output format).

### 4. Rubric
| Criterion | Points |
|---|---|
| 100 runs with real measurements | 3 |
| Clear latency/token/cost numbers | 2 |
| An optimized re-run with improvement shown | 3 |
| Explanation of where efficiency came from | 2 |
