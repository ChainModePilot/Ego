# Design Principles

1. **Out-of-the-Box** — Regular users can get started with three commands after `pip install`. No machine learning knowledge or GPU driver configuration required.
2. **Fully Automated** — From data collection to model output, no manual intervention needed. Like a washing machine: put in clothes, press start, take out clean clothes.
3. **Multi-Version Adaptation** — One training run produces multiple versions for different computing capabilities. The same "you" can simultaneously live on a smartwatch, phone, and computer.
4. **Repeatable Execution** — Retrain after data updates, and Ego evolves accordingly. As you grow, Ego grows too.
5. **Swappable Base** — Switch base models when new ones are released, retrain LoRA. Like upgrading a phone's processor — performance improves but "personality" stays the same.
6. **Plugin Extensible** — Data source adapters, base models, and editions are all extensible. The community can contribute new data source plugins (e.g., Notion adapter, WhatsApp chat history importer).
7. **Privacy First** — All training can be completed locally, data never leaves the device. Your diary, chat history, and work notes stay forever on your own computer.
