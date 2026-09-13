# SoCLaaS Lab 103 — RAG Mini-Project (Chat With a Document)

Course-agnostic lab on Retrieval-Augmented Generation. Students load a small document, retrieve relevant chunks, and ground answers in it.

---

## Lesson Plan

- **Objective:** Students understand retrieval-augmented generation: ground answers in source data instead of letting the model guess.
- **Duration:** ~2–3 hrs, one module (or part of a capstone).
- **Flow:** key → split a document into chunks → retrieve the 3 most relevant → pass to the API → answer.
- **Integrity note:** disclose LLM use; graded on retrieval quality + grounding.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code (pure-Python; no dependencies)
```python
import os, math, requests, re

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
url = SOCLAAS_BASE_URL + "/chat/completions"

# --- tiny "embedding" via bag-of-words on the query ---
def tokenize(t):
    return re.findall(r"\w+", t.lower())

def qvec(qterms, docs):
    V = {w: i for i, w in enumerate(sorted({w for d in docs for w in tokenize(d)}))}
    def vec(text):
        v = [0]*len(V)
        for w in tokenize(text):
            v[V[w]] += 1
        return v
    return vec(qterms), v, V

# --- cosine similarity ---
def cosine(a, b):
    n = sum(x*y for x, y in zip(a, b))
    return n / (math.sqrt(sum(x*x for x in a)) * math.sqrt(sum(y*y for y in b)) or 1)

# --- load one document (a class reading, paper, or textbook chapter) ---
TEXT = open("reading.txt").read()
CHUNKS = [TEXT[i:i+600] for i in range(0, len(TEXT), 600)]

# --- retrieve top chunks for a question, then ground the answer ---
question = "What is quantum entanglement?"
qvec_, allvecs, _ = qvec(question, CHUNKS)
scored = sorted(enumerate(CHUNKS),
                key=lambda c: cosine(qvec_, allvecs[c[0]]), reverse=True)

context = "\n\n".join(CHUNKS[i] for i in [0, 1, 2])   # top 3 chunks

resp = requests.post(url, headers={"Authorization": f"Bearer {API_KEY}"},
    json={"model": "default", "messages": [
        {"role": "system", "content": "Answer ONLY using the provided context. If it is not there, say so."},
        {"role": "user", "content": f"Context:\n{context}\n\nQuestion: {question}"},
    ]}, timeout=30)
print(resp.json()["choices"][0]["message"]["content"])
```

### 3. The Challenge
Pick a document from your own course. Build the RAG pipeline, ask 3 course questions, and for each: (a) which chunks were retrieved, (b) the grounded answer. Then explain **one failure mode** where the model went outside the context (and how to prevent it).

### 4. Rubric
| Criterion | Points |
|---|---|
| Retrieval returns relevant chunks | 3 |
| Answers grounded in retrieved context | 3 |
| At least one failure mode identified | 2 |
| Reflection on when RAG helps/hurts | 2 |
