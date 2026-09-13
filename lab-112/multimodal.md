# SoCLaaS Lab 112 — Multi-Modal (Image Understanding)

Course-agnostic lab on multimodal input. Students send an image + a question to the vision-capable model `qwen3.8:27b` and build a tool around image understanding (e.g. grading a screenshot of code).

---

## Lesson Plan

- **Objective:** Students use the vision-capable model to process an image alongside a text question, then wrap it in a reusable tool.
- **Duration:** ~2 hrs.
- **Flow:** key → encode an image to base64 → send image + question → build an image-question tool.
- **Integrity note:** disclose LLM use; graded on the tool design.

---

## Student Handout

### 1. Get your key
`https://soclaas-portal.comp.nus.edu.sg` → login → issue key.

### 2. Starter code (multimodal)
```python
import os, base64, requests

API_KEY = os.environ["SOCLAAS_API_KEY"]   # standard SoCLaaS env var (issue at soclaas-portal.comp.nus.edu.sg)
SOCLAAS_BASE_URL = "https://soclaas-api.comp.nus.edu.sg/v1"
URL = SOCLAAS_BASE_URL + "/chat/completions"

def call(prompt, image_path=None):
    content = [{"type": "text", "text": prompt}]
    if image_path:
        with open(image_path, "rb") as f:
            b64 = base64.b64encode(f.read()).decode()
        content.append({"type": "image_url",
                        "image_url": {"url": f"data:image/png;base64,{b64}"}})
    resp = requests.post(URL, headers={"Authorization": f"Bearer {API_KEY}"},
        json={"model": "qwen3.8:27b",   # vision-capable model
              "messages": [
                  {"role": "system", "content": "You see images and answer questions about them."},
                  {"role": "user", "content": content}], timeout=60})
    return resp.json()["choices"][0]["message"]["content"]

# --- example: grade a screenshot of a code file ---
print(call("What language is this, and what is the bug?", image_path="code.png"))
```

### 3. The Challenge
Capture a screenshot (a screenshot of code, a math problem, a diagram, or an error message). Build a small tool that ingests an image and answers a question about it. Try three different images and compare the model's accuracy on each. Explain one case where the image was misinterpreted — and how you'd fix it.

### 4. Rubric
| Criterion | Points |
|---|---|
| Image correctly sent + understood | 3 |
| Tool handles 3 image cases | 3 |
| One misinterpretation identified | 2 |
| Reflection on multimodal limits | 2 |

> **Note:** the exact `image_url` schema and model name may shift with the backend's substituted model. Run one request first, inspect the JSON, and adapt.
