# SoCLaaS Lab 105 — Build An Agent

Course-agnostic lab on building a small agent loop: observe → plan → act with a tool → observe. Portfolio-worthy; best for ambitious students / capstone.

---

## Lesson Plan

- **Objective:** Students build an agent that calls tools (calculator, search, database) to solve a multi-step task.
- **Duration:** ~3 hrs (or a capstone).
- **Flow:** key → define a few tools → agent loop that decides which tool to call → observe results → iterate.
- **Integrity note:** disclose LLM use; graded on the loop design and tool use.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code (agent loop)
```python
import os, requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
url = SOCLAAS_BASE_URL + "/chat/completions"

# --- tools the agent can call ---
def tools(name, **args):
    if name == "calc":   return round(float(args["expr"]), 4)
    if name == "weather": return {"loc": args["loc"], "temp": "unknown"}  # stub; swap for a real lookup
    if name == "search": return [{"title": args["q"], "url": "https://duckduckgo.com/?q=" + args["q"]}]
    return None

TOOLS = [
    {"type": "function", "function": {"name": "calc", "parameters": {"type": "object", "properties": {"expr": {"type": "string"}}}}},
    {"type": "function", "function": {"name": "weather", "parameters": {"type": "object", "properties": {"loc": {"type": "string"}}}}},
    {"type": "function", "function": {"name": "search", "parameters": {"type": "object", "properties": {"q": {"type": "string"}}}}},
]

def call_model(messages):
    return requests.post(url, headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": "default", "messages": messages, "tools": TOOLS}, timeout=30)

# --- the loop: model decides -> we run the tool -> feed result back ---
messages = [{"role": "user", "content": "How much is 12 * 8, then what's the weather in Paris?"}]
for _ in range(5):
    resp = call_model(messages)
    msg = resp.json()["choices"][0]["message"]
    if not msg.get("tool_calls"):
        print("ANSWER:", msg["content"]); break
    messages.append(msg)
    for tc in msg["tool_calls"]:
        fn = tc["function"]["name"]
        args = json.loads(tc["function"]["arguments"])
        messages.append({"role": "tool", "tool_call_id": tc["id"], "content": str(tools(fn, **args))})
```
> **Note:** the exact `tool_calls` / `arguments` JSON shape depends on the model the backend substitutes. Inspect one response and adapt the loop. With `model: "default"` you'll likely see a specific structure — capture it, then write the loop to match.

### 3. The Challenge
Build an agent that solves a real multi-step task from your own field (e.g. "for these 5 students, average the scores and check attendance"). Give it 2–3 tools. Show the tool-calling trace and explain where the agent got confused — then fix it.

### 4. Rubric
| Criterion | Points |
|---|---|
| Agent loop runs multiple steps | 3 |
| At least one real tool call works | 3 |
| Trace shows observation → planning | 2 |
| One failure + how you fixed it | 2 |
