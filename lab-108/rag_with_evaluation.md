# SoCLaaS Lab 108 — RAG With Evaluation

Course-agnostic lab on building a retrieval-augmented pipeline *and* grading it. Students stop guessing whether retrieval works and measure it.

---

## Lesson Plan

- **Objective:** Students build a RAG system and an evaluation harness that measures retrieval accuracy and answer faithfulness.
- **Duration:** ~3 hrs (or part of a capstone).
- **Flow:** key → chunk a document → retrieve for a Q&A set → grade retrieval (did the right chunk come back?) and answers (did it stay in-context?) → report.
- **Integrity note:** disclose LLM use; graded on the evaluation methodology.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code
```python
import os, math, re, requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
URL = SOCLAAS_BASE_URL + "/chat/completions"

def ask(prompt):
    resp = requests.post(URL, headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": "default", "messages": [
            {"role": "system", "content": "Answer only from the context."},
            {"role": "user", "content": prompt}], timeout=30})
    return resp.json()["choices"][0]["message"]["content"]

# --- load one document, chunk it ---
TEXT = open("reading.txt").read()
CHUNKS = [TEXT[i:i+500] for i in range(0, len(TEXT), 500)]

# --- trivial "embedding": cosine over token frequency ---
def vec(text):
    terms = re.findall(r"\w+", text.lower())
    V = {}
    for w in terms:
        V[w] = V.get(w, 0) + 1
    keys = sorted(V)
    return [V.get(k, 0) for k in keys], keys

def cosine(a, b):
    n = sum(x*y for x, y in zip(a, b))
    return n / (math.sqrt(sum(x*x for x in a)) * math.sqrt(sum(y*y for y in b)) or 1)

# --- Q&A set the student writes (ground truth) ---
QA = [
    ("question 1", "golden answer 1"),
    ("question 2", "golden answer 2"),
]

def retrieval(q):
    vq, _ = vec(q)
    scored = sorted(enumerate(CHUNKS),
                    key=lambda c: cosine(vq, vec(c[1]))[0], reverse=True)
    return [c[1] for c in scored[:3]]

# --- grade retrieval: is any golden chunk in the top-3? ---
hits, total = 0, 0
for q, golden in QA:
    top3 = retrieval(q)
    # a chunk counts if it contains a keyword from the golden answer
    def overlaps(chunk):
        gk = {w for w in re.findall(r"\w+", golden.lower()) if len(w) > 4}
        ck = {w for w in re.findall(r"\w+", chunk.lower())}
        return bool(gk & ck)
    if any(overlaps(c) for c in top3):
        hits += 1
    total += 1

print("retrieval accuracy: %.0f%%" % (100 * hits / total))

# --- grade faithfulness: does the answer contain golden keywords? ---
faith = 0
for q, golden in QA:
    ans = ask(f"Context:\n{chr(10).join(retrieval(q))}\n\nQ: {q}")
    gk = {w for w in re.findall(r"\w+", golden.lower()) if len(w) > 4}
    ak = {w for w in re.findall(r"\w+", ans.lower())}
    faith += 1 if bool(gk & ak) else 0
print("answer faithfulness: %.0f%%" % (100 * faith / total))
```
> **Note:** the keyword-overlap metric is a *stand-in* for true embeddings. Swap in a real embedding model for a stronger grader.

### 3. The Challenge
Write a Q&A set from your own course document. Build the RAG pipeline, compute retrieval accuracy and answer faithfulness, then improve your retrieval (e.g. better chunk size, query rewrite, more top-k) and show the numbers go up.

### 4. Rubric
| Criterion | Points |
|---|---|
| RAG pipeline retrieves relevant chunks | 3 |
| Evaluation harness computes real metrics | 3 |
| Measured improvement after tuning | 2 |
| Reflection on what the metrics miss | 2 |
