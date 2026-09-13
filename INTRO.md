# SoCLaaS Lab Kit — Intro

A short primer before the labs. Read this first, then pick a lab to start. It costs ~10 minutes and pays off tenfold: the hands-on code stops being magic and becomes something you can debug and extend.

---

## What is an LLM API call?

At its core, the SoCLaaS API is one operation: **send messages, get a completion back.** You POST a list of messages to the endpoint and receive the model's text reply. That's it. Every lab builds on this single call.

A **message** is a small pair:

```json
{"role": "user", "content": "Explain quantum entanglement in one sentence."}
```

Roles you'll meet:

- **`system`** — the instructions the model keeps "in mind" (its personality, the rules, the source context). Not part of the question; it shapes how the answer is formed.
- **`user`** — what you or the program are asking.

The request is a small JSON document:

```json
{
  "model": "default",
  "messages": [ {"role": "system", "content": "..."}, {"role": "user", "content": "..."} ],
  "timeout": 60
}
```

The endpoint:

```
https://soclaas-api.comp.nus.edu.sg/v1/chat/completions
```

You authenticate by sending your key as a bearer token in the `Authorization` header:

```python
requests.post(URL, headers={"Authorization": f"Bearer {API_KEY}"}, json=payload)
```

A **successful response** looks roughly like:

```json
{"choices": [{"message": {"role": "assistant", "content": "the model's text"}}]}
```

A **failed response** (network down, bad key, timeout) does not throw cleanly in the code — it comes back in the response body or as an exception you can inspect. Two labs (105, 112) build the "read the error" pattern so a bad call is diagnosable, not just a crash.

### A few words that show up everywhere

- **Token** — the smallest unit the model "sees," roughly a chunk of a word. A document is many tokens; a single short question is a few.
- **Tokens / input** — text you send *to* the model.
- **Tokens / output** — text the model produces. Longer outputs cost more and take longer.
- **Temperature** — creativity control. Low = focused, factual. High = more surprising, varied. The labs mostly leave it at the default.
- **Latency** — how long a call takes to come back. You'll measure and compare it in Lab 106.

### `model: "default"` — why labs don't hardcode a model name

Every lab sends `"model": "default"` instead of a specific model name. This is deliberate: it lets the lab keep working as SoCLaaS's available models change. Because you don't pin a model name, an upgrade, rename, or retirement of a model on the platform never breaks your starter code — the platform resolves `default` to a current, working model. You never hardcode a model name into the lab. Lab 107 exists to let you compare a couple of *real* models side by side (there you swap in their names); Lab 112 deliberately opts into a specific vision-capable model (`qwen3.8:27b`) because it needs image input, which only some models support, and `default` isn't guaranteed to be vision-capable.

---

## The twelve labs — read as one arc

They build on each other. Beginner labs teach the raw mechanics; Advanced labs layer real-world concerns (measurement, safety, memory) on top.

- **Lab 101 — Prompt Engineering & Agent Skills.** Your first API call, how to craft and compare prompts, and a tiny tool-calling loop. *(Starting point.)*
- **Lab 102 — Prompt Comparison.** Ask the same question three ways and see how the output changes.
- **Lab 103 — RAG Mini-Project (Chat With a Document).** Ground answers in a source document instead of letting the model guess. *(RAG = retrieval-augmented generation.)*
- **Lab 104 — AI As Critic & Tutor.** Have the model grade your own work against a rubric, then revise.
- **Lab 105 — Build an Agent.** Make a program that calls external tools (calculator, weather, web search) on your behalf.
- **Lab 106 — Cost & Latency Detective.** Measure tokens, time, and cost so you understand what every call is actually costing.
- **Lab 107 — Model Wars.** Compare outputs across different models and judge which is "better" for a task.
- **Lab 108 — RAG With Evaluation.** Not just build a RAG pipeline, but *measure* whether retrieval and answers are actually good.
- **Lab 109 — Multi-Agent Systems.** Two agents — a planner and a reviewer — that talk to each other to produce better work.
- **Lab 110 — Agent Memory.** The API is stateless; build a layer so the agent remembers facts across turns.
- **Lab 111 — Guardrails.** Defend a system against prompt injection — tricks that try to get the model to ignore its instructions.
- **Lab 112 — Multi-Modal (Image Understanding).** Send an image and a question to a vision-capable model.

---

## Before you start

- **Key.** Log in at `https://soclaas-portal.comp.nus.edu.sg` → "Issue API Key" or "Rotate API Key". Add it to `SOCLAAS_API_KEY = "..."` in any starter code.
- **Files.** Each lab folder holds its own handout (`*.md`) and any sample data it needs (`reading.txt`, `my_essay.txt`, `code.png`) placed beside it. Run the code from *inside* the lab folder.
- **No dependencies.** Everything is pure Python 3 plus the standard library and `requests`. If you don't have `requests`, one `pip install requests` covers it.
- **Start with Lab 101** unless you already know the mechanics — you can jump to any other lab, but 101 covers everything you'll need from here on.

> **Instructors:** reference solutions for each lab are kept separately (not in this folder) so students see the challenge without an answer key. Ask for them if you want a reference.
