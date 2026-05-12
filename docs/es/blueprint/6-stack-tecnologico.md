# Stack Tecnológico

| Componente | Tecnología | Justificación |
|------------|-----------|---------------|
| Lenguaje de Programación | Python | Ecosistema de IA/ML más maduro |
| Framework de Entrenamiento | Unsloth / PEFT / MLX | Auto-adaptación multi-backend, cubriendo GPU NVIDIA, Apple Silicon, CPU |
| Método de Entrenamiento | QLoRA (PEFT) | Cuantización de 4 bits, entrenamiento en una sola GPU, compatible con hardware de consumo |
| Formato de Modelo | SafeTensors | Seguro (sin riesgo de pickle), carga rápida, estándar de HuggingFace |
| Runtime de Inferencia | llama.cpp / Ollama | Multiplataforma, ligero, soporte de modelos cuantizados |
| Formato de Configuración | YAML | Legible por humanos, fácil de editar |
| Gestión de Paquetes | pip / conda | Ecosistema estándar de Python |
