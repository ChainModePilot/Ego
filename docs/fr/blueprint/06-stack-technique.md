# Stack technique

| Composant | Technologie | Justification |
|-----------|-------------|---------------|
| Langage de programmation | Python | Écosystème IA/ML le plus mature |
| Framework d'entraînement | Unsloth / PEFT / MLX | Auto-adaptation multi-backend, couvrant GPU NVIDIA, Apple Silicon, CPU |
| Méthode d'entraînement | QLoRA (PEFT) | Quantification 4 bits, entraînement sur GPU unique, compatible avec le matériel grand public |
| Format de modèle | SafeTensors | Sûr (pas de risque pickle), chargement rapide, standard HuggingFace |
| Runtime d'inférence | llama.cpp / Ollama | Multi-plateforme, léger, support des modèles quantifiés |
| Format de configuration | YAML | Lisible par l'humain, facile à éditer |
| Gestion des packages | pip / conda | Écosystème standard Python |
