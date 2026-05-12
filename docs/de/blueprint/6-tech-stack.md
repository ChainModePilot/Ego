# Tech-Stack

| Komponente | Technologie | Begründung |
|-----------|------------|-----------|
| Programmiersprache | Python | Ausgereiftestes KI/ML-Ökosystem |
| Trainingsframework | Unsloth / PEFT / MLX | Multi-Backend-Auto-Anpassung, deckt NVIDIA GPU, Apple Silicon, CPU ab |
| Trainingsmethode | QLoRA (PEFT) | 4-Bit-Quantisierung, Training auf einzelner GPU, verbraucherfreundliche Hardware |
| Modellformat | SafeTensors | Sicher (kein Pickle-Risiko), schnelles Laden, HuggingFace-Standard |
| Inferenz-Laufzeit | llama.cpp / Ollama | Plattformübergreifend, leichtgewichtig, Unterstützung quantisierter Modelle |
| Konfigurationsformat | YAML | Menschenlesbar, einfach zu bearbeiten |
| Paketverwaltung | pip / conda | Python-Standard-Ökosystem |
