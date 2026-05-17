# Architecture

## Overall Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Ego Pod (Self-Awareness Capsule)              │
│                                                                 │
│  ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌────────┐ │
│  │  Data      │───→│  Corpus   │───→│  Training │───→│Version │ │
│  │ Collector  │    │ Compiler  │    │  Engine   │    │ Vault  │ │
│  └───────────┘    └───────────┘    └───────────┘    └────────┘ │
│       ↑                                                  ↓      │
│  ┌─────────┐                                      ┌──────────┐ │
│  │  Source  │                                      │ Ego      │ │
│  │  Config  │                                      │ Editions │ │
│  └─────────┘                                      └──────────┘ │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  Config Center                            │   │
│  │  Base Selection · Edition Defs · Training Params · Source Mapping · Schedule │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

## Module Details

### 1. Data Collector

Reads Human Prime data from configured data sources.

**Supported Data Source Types:**
- Local directories (filesystem paths)
- URLs (HTTP/HTTPS endpoints)
- Cloud storage (S3, OSS, Google Drive, etc., extensible via plugins)
- Custom adapters (users can write collection plugins)

**Data Categories Collected (corresponding to iFay architecture):**

| Data Category | iFay Module | Real-World Example |
|--------------|-------------|-------------------|
| Perception Data | Sense | Daily exercise routes, phone usage time distribution, frequently visited restaurant locations, sleep patterns |
| Behavioral Data | Action | Phone operation habits (always check messages before email), frequently used shortcuts, automation scripts |
| Cognitive Data | Thought | Reading notes, work journals, learning reflections, evaluations and thought processes on technical solutions |
| Skill Data | Skill | Programming languages and proficiency levels, favorite recipes, instruments played and repertoire |
| Credential Data | Credentials | Identity identifiers, authorization scope (e.g., can send emails on your behalf but cannot make transfers), permission boundaries |

> For example: A freelance translator's Collector configuration might look like this — Sense points to her translation memory (local directory), Action points to her CAT tool operation logs (URL), Thought points to her terminology notes (cloud storage), Skill points to her language pairs and specialization list (local file).

The Collector doesn't care about the internal format of data (that's defined by sister projects) — it only cares about "where to fetch" and "what category of data was fetched."

### 2. Corpus Compiler

Transforms collected raw data into corpus usable for LoRA training.

**Core Responsibilities:**
- **Data Cleansing & Anonymization** — Especially for Credentials-related data. Example: Your API key `sk-abc123...` gets replaced with `[REDACTED]`, ensuring no sensitive information in training data
- **Format Standardization** — Unified into formats acceptable by training frameworks. Example: A reading note "This book is about the CAP theorem in distributed systems..." gets transformed into `{"instruction": "Explain the CAP theorem", "output": "The CAP theorem in distributed systems..."}`
- **Corpus Classification** — Grouped by LoRA layers. Example: "I like drinking Americano" goes into the preference layer, "Traveled to Japan in 2024" into the memory layer, "Python's GIL mechanism is..." into the knowledge layer
- **Quality Filtering** — Remove noise data and duplicates. Example: Duplicate chat records keep only one copy, overly short fragments (like "ok", "sure") are filtered out
- **Data Augmentation** — Expand corpus through templates or rewriting when necessary. Example: Raw data "Recommend a song" gets augmented to "Express in a different way: Recommend a song", "Rephrase: Recommend a song"

**Output Formats:**
- Standard instruction fine-tuning dataset (instruction/input/output triplets)
- Conversation format dataset (multi-turn conversation)
- Sub-datasets grouped by LoRA layers


### 3. Training Engine (Trainer)

Trains LoRA adapters based on the selected base model and compiled corpus.

**Multi-Backend Auto-Adaptation:**

The Trainer automatically detects your hardware environment and selects the optimal training backend:

| Priority | Backend | Environment | Speed |
|----------|---------|-------------|-------|
| 1 | Unsloth | NVIDIA GPU (CUDA) | Fastest (2x speedup) |
| 2 | HuggingFace PEFT | NVIDIA GPU (CUDA) | Standard speed |
| 3 | Apple MLX | Mac (Apple Silicon) | Native acceleration |
| 4 | HuggingFace PEFT | CPU only | Slower but universal |
| 5 | Stub | No ML dependencies | Simulation mode (dev/test) |

> You don't need to worry about which backend is used — Ego Pod selects automatically. If you have an RTX 4090, it uses Unsloth for acceleration; if you're on a MacBook Pro M3, it uses MLX; if you just want to try the flow first, it uses Stub mode for simulated training.

**Training Flow:**
1. Load base model (4-bit quantization)
2. Train adapters sequentially by LoRA layer
3. Validate training results (automatic alignment evaluation)
4. Generate base+LoRA combinations for each edition

**Multi-Edition Training Strategy:**
- Each edition uses a different scale base model
- Same corpus, LoRA trained separately for different bases
- Multiple editions can be trained in parallel

> For example: On a desktop with a GPU, running `ego train` will have Ego Pod simultaneously train three versions of LoRA for your smartwatch (lite), phone (normal), and computer (plus). The entire process takes about 10-30 minutes, depending on data volume.

### 4. Version Vault

Manages Ego model versions and outputs.

**Responsibilities:**
- Store LoRA adapters produced by each training run
- Manage model files across multiple editions
- Support version rollback (preserve historical versions)
- Overwrite old versions (default behavior)
- Export as distributable Ego packages

> For example: You trained an Ego last week (v0.1.0), then added new data and retrained this week (v0.2.0). You find the new version's response style has changed — it doesn't sound like you anymore. You can roll back to v0.1.0 with one click, like downgrading a phone's OS.

**Ego Package Structure:**
```
ego-output/
├── manifest.json              # Ego metadata (version, base info, edition list)
├── lite/
│   ├── config.json            # Edition config (base model, quantization, hardware requirements)
│   └── adapters/
│       └── ego-lora.safetensors  # LoRA adapter weights
├── normal/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
├── plus/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
├── super/
│   ├── config.json
│   └── adapters/
│       └── ego-lora.safetensors
└── [custom-name]/             # User-defined edition
    ├── config.json
    └── adapters/
        └── ego-lora.safetensors
```

### 5. Config Center

Centrally manages all Ego Pod configurations.

**Configuration Items:**

```yaml
# ego-config.yaml example

# Data source mapping — tell Ego Pod where to find your data
sources:
  sense:
    type: "local"
    path: "/data/ifay/sense/"          # Your perception data directory
  action:
    type: "url"
    endpoint: "https://api.example.com/action-logs"  # Behavioral log API
  thought:
    type: "local"
    path: "/data/ifay/thought/"        # Your notes and knowledge base
  skill:
    type: "s3"
    bucket: "ifay-skills"              # Cloud skill data
    prefix: "user-001/"
  credentials:
    type: "local"
    path: "/data/ifay/credentials/"    # Credential data (auto-anonymized)

# Training parameters — usually no modification needed, defaults suit most scenarios
training:
  method: "qlora"
  quantization: "4bit"
  lora_rank: 16
  lora_alpha: 32
  learning_rate: 2e-4
  epochs: 3
  batch_size: 4

# Edition definitions — which versions you want to produce
editions:
  lite:
    base: "qwen3.5-0.8B"
    description: "Smartwatches, voice recorders, drones, IoT"
  normal:
    base: "qwen3.5-2B"
    description: "Phones, tablets"
  plus:
    base: "qwen3.5-4B"
    description: "High-performance PCs, local servers"
  super:
    base: "qwen3.5-27B"
    description: "Cloud services, GPU clusters"
  # You can add custom editions here, e.g.:
  # clinic:
  #   base: "qwen3.5-4B"
  #   description: "Outpatient clinic edition"

# Schedule strategy — when to automatically retrain
schedule:
  auto_update: true
  interval: "weekly"          # Auto-forge weekly
  trigger: "data_change"      # Or manual / cron (scheduled task)
```
