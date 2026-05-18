# Tech Stack

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Programming Language | Python | Most mature AI/ML ecosystem |
| Training Framework | Unsloth / PEFT / MLX | Multi-backend auto-adaptation, covering NVIDIA GPU, Apple Silicon, CPU |
| Training Method | QLoRA (PEFT) | 4-bit quantization, single GPU training, consumer hardware friendly |
| Model Format | SafeTensors | Safe (no pickle risk), fast loading, HuggingFace standard |
| Inference Runtime | llama.cpp / Ollama | Cross-platform, lightweight, quantized model support |
| Configuration Format | YAML | Human-readable, easy to edit |
| Package Management | pip / conda | Python standard ecosystem |
