# soclaas-lab - SoCLaaS Lab Kit

Turnkey course-agnostic labs so any faculty can drop SoCLaaS into their course with zero prep. All labs run against the SoCLaaS API with `model: "default"` (backend substitutes the model — examples keep working across model upgrades).

## Get started (1 minute)
1. Go to `https://soclaas-portal.comp.nus.edu.sg`, login, click "issue key".
2. Add the key to `API_KEY = "***"` in any starter code.
3. API endpoint: `https://soclaas-api.comp.nus.edu.sg/v1/chat/completions`

## The labs

| Kit | Focus | Difficulty |
|---|---|---|
| [Lab 101](lab-101/prompt_engineering.md) | Prompt engineering + first API call + a tiny agent (the base kit) | Beginner |
| [Lab 102](lab-102/prompt_challenge.md) | Prompt engineering — craft 3 prompts for one question, compare outputs | Beginner |
| [Lab 103](lab-103/rag_miniproject.md) | RAG mini-project — chat with a document | Intermediate |
| [Lab 104](lab-104/ai_as_critic.md) | AI as critic/tutor — review your own work | Beginner |
| [Lab 105](lab-105/build_an_agent.md) | Build an agent that calls external tools | Advanced |
| [Lab 106](lab-106/cost_latency_detective.md) | Measure tokens, latency, and cost | Beginner |
| [Lab 107](lab-107/model_wars.md) | Compare outputs across models | Beginner |
| [Lab 108](lab-108/rag_with_evaluation.md) | RAG with evaluation — retrieval accuracy + answer faithfulness | Advanced |
| [Lab 109](lab-109/multi_agents.md) | Multi-agent systems — planner↔reviewer agent loop | Advanced |
| [Lab 110](lab-110/agent_memory.md) | Agent memory — persistent facts across turns | Advanced |
| [Lab 111](lab-111/guardrails.md) | Guardrails — prompt-injection defense (cybersecurity) | Advanced |
| [Lab 112](lab-112/multimodal.md) | Multimodal image understanding (qwen3.8:27b) | Advanced |

## Instructor solutions
Reference solutions for each lab are **not published here** — the kit ships hands-on only, so students see the challenge without an answer key. Instructors who want a reference solution for a lab can request it separately.

**Design principle:** every lab teaches transferable skills (prompting, API use, agents, evaluation), not subject content — so a faculty member can reuse it as-is without editing for their course.

## Recommended rollout
- **Broad reach:** Lab 102 (prompting) and Lab 106 (cost) — dead simple, course-agnostic.
- **Research-heavy courses:** Lab 103 (RAG).
- **Ambitious students / capstone:** Lab 105 (agent), Lab 107 (model wars).
