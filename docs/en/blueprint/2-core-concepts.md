# Core Concepts

## Ego Pod (Self-Awareness Capsule)

Ego Pod is the core engine of Ego — a fully automated training pipeline. Like a "cultivation chamber," you put raw data in, and it automatically forges an AI model that understands you.

It does three things:
1. **Collect** — Read Human Prime data from specified sources (local directories/URLs/cloud storage/...)
2. **Forge** — Process raw data into training corpus, train LoRA adapters on a selected base model
3. **Output** — Produce multiple versions (editions) of the Ego model, overwriting old versions

> Analogy: Ego Pod is like a "personalization factory." You feed in your diary, chat history, and work notes (raw materials), and it automatically cleans, classifies, and processes them, ultimately producing a "mini version of you" (finished product). This product can be installed on a smartwatch (lite edition) or a computer (plus edition), like the same person wearing different sized clothes.

```
Data Sources               Ego Pod                        Output
┌──────────────┐        ┌─────────────────┐         ┌──────────────┐
│ Sense Data    │──┐     │                 │         │ Ego-lite     │
│ Action Data   │──┤     │  ① Data Collection│        │ Ego-normal   │
│ Thought Data  │──┼───→ │  ② Corpus Building│───────→│ Ego-plus     │
│ Skill Data    │──┤     │  ③ LoRA Training  │        │ Ego-super    │
│ Credentials  │──┘     │  ④ Multi-ver Output│        │ [custom...]  │
└──────────────┘        └─────────────────┘         └──────────────┘
 Directory/URL/Cloud       Fully Auto · Repeatable       Overwrites Old
```

## Ego Model Structure

Each Ego = Base Model + LoRA Adapter

- **Base Model**: Open-source small-parameter models (e.g., Qwen 3.5, Gemma 4), providing general language and reasoning capabilities. Analogy: The base is like a "bare-bones apartment" — it has basic structure and utilities, but no personal style.
- **LoRA Adapter**: Trained from Human Prime data, carrying the Human Prime's personality, preferences, memories, knowledge, and skills. Analogy: LoRA is like your "interior design plan" — your favorite furniture, wall colors, and lighting layout are what make the house "your home."

The base contains no personalized information; LoRA is the "soul."

> Switching bases is like moving: You move from a studio (0.8B) to a three-bedroom (4B) — the house gets bigger, but your interior style (LoRA) needs to be re-adapted to the new layout. The good news is that Ego Pod automatically handles this "renovation" process.

## Edition System

Ego provides four default versions, adapted to endpoints with different computing capabilities:

| Edition | Target Device | Base Scale | Typical Scenario |
|---------|--------------|-----------|-----------------|
| **lite** | Smartwatches, voice recorders, drones, IoT devices | ~0.8B | Ego-lite runs on your smartwatch, knows you wake at 7 AM and prefer black coffee, automatically pushes weather and schedule reminders each morning in the same tone as your own memos |
| **normal** | Phones, tablets | ~2B | Ego-normal on your phone understands your conversation habits, automatically uses your common phrasing and signature format when drafting emails |
| **plus** | High-performance PCs, local servers | ~4-9B | Ego-plus on your laptop assists with coding, having learned your coding style (variable naming conventions, comment style, common design patterns), giving suggestions as if you wrote them yourself |
| **super** | Cloud services, GPU clusters | ~27B+ | Ego-super in the cloud has your complete knowledge base, can attend technical reviews on your behalf, giving opinions highly consistent with your own judgment |

Custom editions are supported — users can name and configure them freely.

> For example: A doctor can define a custom "clinic" edition (base 4B), specialized for outpatient scenarios, having learned their consultation habits, common prescription templates, and patient communication style.

Each edition uses a different scale base model and has its own independently trained set of LoRA adapters.
